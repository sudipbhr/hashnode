---
title: "How to Build a Login and Registration API with ASP.NET Web API"
seoTitle: "Build Login & Registration API with ASP.NET"
seoDescription: "Learn to implement secure JWT authentication in ASP.NET Core Web API using Entity Framework Core, PostgreSQL, and ASP.NET Core Identity"
datePublished: Mon Apr 14 2025 17:47:12 GMT+0000 (Coordinated Universal Time)
cuid: cm9hd911d000309juhnty7um0
slug: how-to-build-a-login-and-registration-api-with-aspnet-web-api
tags: authentication, aspnet-core, jwt-authentication-aspnet

---

Authentication is a critical part of any modern web application. In this tutorial, we'll walk you through implementing a secure JWT (JSON Web Token) authentication system in an [ASP.NET](http://ASP.NET) Core Web API project. We'll use Entity Framework Core with PostgreSQL and [ASP.NET](http://ASP.NET) Core Identity for user management.

## What We'll Build

By the end of this tutorial, you'll have:

1. A complete authentication system with registration and login endpoints
    
2. JWT token generation and validation
    
3. Role-based authorization
    
4. Protected API endpoints
    

## Prerequisites

* .NET 6.0 or higher
    
* Basic understanding of [ASP.NET](http://ASP.NET) Core
    
* PostgreSQL database (or you can adapt to another DB provider)
    
* Visual Studio or VS Code
    

## Project Setup

Let's start by setting up our project structure. We'll need the following components:

1. Models for authentication
    
2. DbContext for Entity Framework Core
    
3. Token generation service
    
4. Authentication controllers
    
5. Protected API endpoints
    

## Required Packages

Install following required packages

1. Microsoft.EntityFrameworkCore.Tools
    
2. Microsoft.AspNetCore.Identity
    
3. Microsoft.AspNetCore.Authentication.JwtBearer
    

## Step 1: Setting Up the User Model and DTOs

First, let's create our user model by extending the IdentityUser class from [ASP.NET](http://ASP.NET) Core Identity:  
***Models/ApplicationUser.cs***

```csharp
// ApplicationUser.cs
using Microsoft.AspNetCore.Identity;

namespace Ecommerce.Models
{
    public class ApplicationUser: IdentityUser
    {
        public string MobileNumber { get; set; }
        public string Address { get; set; }
    }
}
```

Run migrations and update database:

Go to package manager console :  
*1\. add-migration “updated user module”  
2\. update-database*

Next, we'll need DTOs (Data Transfer Objects) for registration and login:  
Create a Folder name ***DTOs/Auth/***

```csharp
//  DTOs/Auth/RegisterDto.cs
public class RegisterDto
{
    public string Email { get; set; }
    public string UserName { get; set; }
    public string Password { get; set; }
    public string MobileNumber { get; set; }
    public string Address { get; set; }
}
```

```csharp
//  DTOs/Auth/LoginDto.cs
public class LoginDto
{
    public string UserName { get; set; }
    public string Password { get; set; }
}
```

```csharp

//  DTOs/Auth/AuthResponseDto.cs
public class AuthResponseDto
{
    public bool IsSuccess { get; set; }
    public string Message { get; set; }
    public string Token { get; set; }
    public string UserName { get; set; }
    public string Email { get; set; }
    public List<string> Roles { get; set; }
}
```

## Step 2: Setting Up the Database Context

Next, we need to create our database context:

```csharp
// Data/ApplicationDbContext.cs
using Ecommerce.Models;
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore;

namespace Ecommerce.Data
{
    public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
}
```

## Step 3: Creating a Token Service

The most important part of JWT authentication is token generation. Let's create a service that will handle this:

```csharp
// Services/TokenService.cs
using System.IdentityModel.Tokens.Jwt;
using System.Security.Claims;
using System.Text;
using Ecommerce.Models;
using Microsoft.AspNetCore.Identity;
using Microsoft.Extensions.Configuration;
using Microsoft.IdentityModel.Tokens;

namespace Ecommerce.Services
{
    public class TokenService
    {
        private readonly IConfiguration _configuration;
        private readonly UserManager<ApplicationUser> _userManager;

        public TokenService(IConfiguration configuration, UserManager<ApplicationUser> userManager)
        {
            _configuration = configuration;
            _userManager = userManager;
        }

        public async Task<string> GenerateToken(ApplicationUser user)
        {
            var jwtTokenHandler = new JwtSecurityTokenHandler();
            var key = Encoding.ASCII.GetBytes(_configuration["JwtConfig:Secret"]);

            var roles = await _userManager.GetRolesAsync(user);
            var claims = new List<Claim>
            {
                new Claim(ClaimTypes.NameIdentifier, user.Id),
                new Claim(JwtRegisteredClaimNames.Sub, user.UserName),
                new Claim(JwtRegisteredClaimNames.Email, user.Email),
                new Claim(JwtRegisteredClaimNames.Jti, Guid.NewGuid().ToString())
            };

            // Add role claims
            foreach (var role in roles)
            {
                claims.Add(new Claim(ClaimTypes.Role, role));
            }
            
            // Set defaults if config values are missing
            var issuer = _configuration["JwtConfig:Issuer"] ?? "DefaultIssuer";
            var audience = _configuration["JwtConfig:Audience"] ?? "DefaultAudience";
            var expiryMinutesStr = _configuration["JwtConfig:ExpiryInMinutes"];
            var expiryMinutes = string.IsNullOrEmpty(expiryMinutesStr) ? 60 : Convert.ToDouble(expiryMinutesStr);

            // Important: Set the NotBefore time to now and Expires to a future time
            var now = DateTime.UtcNow;
            var expires = now.AddMinutes(expiryMinutes);

            var tokenDescriptor = new SecurityTokenDescriptor
            {
                Subject = new ClaimsIdentity(claims),
                NotBefore = now,
                Expires = expires,
                SigningCredentials = new SigningCredentials(
                    new SymmetricSecurityKey(key), SecurityAlgorithms.HmacSha256Signature),
                Issuer = issuer,
                Audience = audience
            };

            var token = jwtTokenHandler.CreateToken(tokenDescriptor);
            return jwtTokenHandler.WriteToken(token);
        }
    }
}
```

## Step 4: Creating the Authentication Controller

Now, let's create the controller that will handle registration and login:

```csharp
// Controllers/AuthController.cs
using Ecommerce.Models;
using Ecommerce.Services;
using Microsoft.AspNetCore.Identity;
using Microsoft.AspNetCore.Mvc;

namespace Ecommerce.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class AuthController : ControllerBase
    {
        private readonly UserManager<ApplicationUser> _userManager;
        private readonly SignInManager<ApplicationUser> _signInManager;
        private readonly RoleManager<IdentityRole> _roleManager;
        private readonly TokenService _tokenService;

        public AuthController(
            UserManager<ApplicationUser> userManager,
            SignInManager<ApplicationUser> signInManager,
            RoleManager<IdentityRole> roleManager,
            TokenService tokenService)
        {
            _userManager = userManager;
            _signInManager = signInManager;
            _roleManager = roleManager;
            _tokenService = tokenService;
        }

        [HttpPost("register")]
        public async Task<IActionResult> Register([FromBody] RegisterDto model)
        {
            if (!ModelState.IsValid)
            {
                return BadRequest(new AuthResponseDto
                {
                    IsSuccess = false,
                    Message = "Invalid request"
                });
            }

            var userExists = await _userManager.FindByNameAsync(model.UserName);
            if (userExists != null)
            {
                return BadRequest(new AuthResponseDto
                {
                    IsSuccess = false,
                    Message = "User already exists"
                });
            }

            var user = new ApplicationUser
            {
                UserName = model.UserName,
                Email = model.Email,
                MobileNumber = model.MobileNumber,
                Address = model.Address,
                SecurityStamp = Guid.NewGuid().ToString()
            };

            var result = await _userManager.CreateAsync(user, model.Password);
            if (!result.Succeeded)
            {
                return BadRequest(new AuthResponseDto
                {
                    IsSuccess = false,
                    Message = "User creation failed: " + string.Join(", ", result.Errors.Select(e => e.Description))
                });
            }

            // Ensure the "Customer" role exists, create it if it doesn't
            if (!await _roleManager.RoleExistsAsync("Customer"))
            {
                await _roleManager.CreateAsync(new IdentityRole("Customer"));
            }

            // Add user to Customer role
            await _userManager.AddToRoleAsync(user, "Customer");

            return Ok(new AuthResponseDto
            {
                IsSuccess = true,
                Message = "User created successfully"
            });
        }

        [HttpPost("login")]
        public async Task<IActionResult> Login([FromBody] LoginDto model)
        {
            if (!ModelState.IsValid)
            {
                return BadRequest(new AuthResponseDto
                {
                    IsSuccess = false,
                    Message = "Invalid request"
                });
            }

            var user = await _userManager.FindByNameAsync(model.UserName);
            if (user == null)
            {
                return Unauthorized(new AuthResponseDto
                {
                    IsSuccess = false,
                    Message = "Invalid username or password"
                });
            }

            var result = await _signInManager.CheckPasswordSignInAsync(user, model.Password, false);
            if (!result.Succeeded)
            {
                return Unauthorized(new AuthResponseDto
                {
                    IsSuccess = false,
                    Message = "Invalid username or password"
                });
            }

            var token = await _tokenService.GenerateToken(user);
            var roles = await _userManager.GetRolesAsync(user);

            return Ok(new AuthResponseDto
            {
                IsSuccess = true,
                Token = token,
                UserName = user.UserName,
                Email = user.Email,
                Message = "Login successful",
                Roles = roles.ToList()
            });
        }
    }
}
```

## Step 5: Creating Protected Endpoints

Finally, let's create some protected endpoints that will require authentication and specific roles:

```csharp
// Controllers/ProductsController.cs
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;

namespace Ecommerce.Controllers
{
    [Authorize]
    [Route("api/[controller]")]
    [ApiController]
    public class ProductsController : ControllerBase
    {
        [HttpGet]
        public IActionResult Get()
        {
            return Ok(new { Message = "This is a protected endpoint" });
        }

        [Authorize(Roles = "Customer")]
        [HttpGet("customer")]
        public IActionResult GetCustomer()
        {
            return Ok(new { Message = "This endpoint is only accessible to users with the Customer role" });
        }
    }
}
```

## Step 6: Configuring the Application

Now, let's configure our application in the Program.cs file:

```csharp
// Program.cs
using Microsoft.EntityFrameworkCore;
using Ecommerce.Models;
using Microsoft.AspNetCore.Identity;
using Microsoft.AspNetCore.Authentication.JwtBearer;
using Ecommerce.Data;
using Microsoft.IdentityModel.Tokens;
using System.Text;
using Ecommerce.Services;

var builder = WebApplication.CreateBuilder(args);

// Add services to the container.
builder.Services.AddDbContext<ApplicationDbContext>(
    options =>
    options.UseNpgsql(
        builder.Configuration.GetConnectionString("DefaultConnection")));

builder.Services.AddIdentity<ApplicationUser, IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>()
    .AddDefaultTokenProviders();
builder.Services.AddScoped<TokenService>();

builder.Services.AddAuthentication(options =>
{
    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
    options.DefaultScheme = JwtBearerDefaults.AuthenticationScheme;
})
.AddJwtBearer(options =>
{
    options.SaveToken = true;
    options.RequireHttpsMetadata = false;
    options.TokenValidationParameters = new TokenValidationParameters
    {
        ValidateIssuer = true,
        ValidateAudience = true,
        ValidateLifetime = true,
        ValidateIssuerSigningKey = true,
        ValidIssuer = builder.Configuration["JwtConfig:Issuer"],
        ValidAudience = builder.Configuration["JwtConfig:Audience"],
        IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(builder.Configuration["JwtConfig:Secret"]))
    };
});

builder.Services.AddControllers();
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();

var app = builder.Build();

// Configure the HTTP request pipeline.
if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}

app.UseHttpsRedirection();

app.UseAuthentication(); // Don't forget this!
app.UseAuthorization();

app.MapControllers();

// Initialize roles
using (var scope = app.Services.CreateScope())
{
    var roleManager = scope.ServiceProvider.GetRequiredService<RoleManager<IdentityRole>>();

    // Ensure Customer role exists
    if (!await roleManager.RoleExistsAsync("Customer"))
    {
        await roleManager.CreateAsync(new IdentityRole("Customer"));
    }
}

app.Run();
```

## Step 7: Configure JWT Settings in appsettings.json

Don't forget to add JWT configuration to your appsettings.json file:

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Host=localhost;Database=EcommerceDb;Username=postgres;Password=yourpassword"
  },
  "JwtConfig": {
    "Secret": "YourSuperSecretKeyWithAtLeast32Characters",
    "Issuer": "YourApiDomain.com",
    "Audience": "YourFrontendDomain.com",
    "ExpiryInMinutes": 60
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

## Security Considerations

When implementing JWT authentication, keep the following security considerations in mind:

1. **Secret Key Protection:** Your JWT secret key should be at least 32 characters long and kept secure. Consider using Azure Key Vault or similar services for production.
    
2. **Token Expiration:** Always set a reasonable expiration time for your tokens. In our example, we set it to 60 minutes.
    
3. **HTTPS:** Always use HTTPS in production to encrypt JWT tokens in transit.
    
4. **Claim Validation:** Validate all claims, including issuer and audience.
    
5. **Refresh Tokens:** For longer sessions, implement refresh tokens instead of extending JWT token expiration.
    

## Testing the Authentication Flow

You can test your authentication system using a tool like Postman:

1. Register a new user via the `/api/Auth/register` endpoint:
    

```json
{
  "email": "user@example.com",
  "userName": "testuser",
  "password": "StrongPassword123!",
  "mobileNumber": "1234567890",
  "address": "123 Test Street"
}
```

2. Login via the `/api/Auth/login` endpoint:
    

```json
{
  "userName": "testuser",
  "password": "StrongPassword123!"
}
```

3. Copy the token from the response and use it in the Authorization header (Bearer token) to access protected endpoints like `/api/Products`.  
      
    Github : [https://github.com/sudipbhr/asp.net-web-api-auth](https://github.com/sudipbhr/asp.net-web-api-auth)
    

## Conclusion

In this tutorial, we've implemented a complete JWT authentication system in [ASP.NET](http://ASP.NET) Core. We've covered user registration, login, token generation, and role-based authorization. This system is a solid foundation for securing your Web API and can be extended with features like refresh tokens, email confirmation, and more advanced authorization policies.