---
title: "Implementing Google OAuth in ASP.NET Web API"
seoTitle: "Google OAuth for ASP.NET Web API"
seoDescription: "Learn how to set up Google OAuth for ASP.NET Web API, configure OAuth credentials, and implement authentication efficiently in your application"
datePublished: Sat Apr 19 2025 16:52:50 GMT+0000 (Coordinated Universal Time)
cuid: cm9ogids0001b09jmejve1rsv
slug: implementing-google-oauth-in-aspnet-web-api
tags: google-auth, oauth-using-aspnet

---

## 1\. Setting Up Google OAuth Credentials

Before writing any code, you need to set up a project in the Google Cloud Console:

1. Go to the [Google Cloud Console](https://console.cloud.google.com/)
    
2. Create a new project or select an existing one
    
3. Navigate to "APIs & Services" &gt; "Credentials"
    
4. Click "Create Credentials" and select "OAuth client ID"
    
5. Configure the OAuth consent screen with your application details
    
6. For application type, select "Web application"
    
7. Add authorized JavaScript origins (e.g., `http://localhost:7276` for development)
    
8. Add authorized redirect URIs
    
9. Create the client ID and note your Client ID and Client Secret
    

## 2\. Configuring Your [ASP.NET](http://ASP.NET) Core Application

Add the necessary NuGet packages to your project:

```csharp
dotnet add package Google.Apis.Auth
dotnet add package Microsoft.AspNetCore.Identity
dotnet add package Microsoft.AspNetCore.Authentication.JwtBearer
```

Update your `appsettings.json` file with the Google OAuth configuration:

```csharp
{
  "Google": {
    "ClientId": "YOUR_GOOGLE_CLIENT_ID",
    "ClientSecret": "YOUR_GOOGLE_CLIENT_SECRET"
  },
  "JwtConfig": {
    "Secret": "YOUR_SECURE_JWT_SECRET_KEY",
    "Issuer": "YourAppName",
    "Audience": "YourAppUsers",
    "ExpiryInMinutes": 60
  }
}
```

## 3\. The Token Service Implementation

The token service is responsible for generating JWT tokens for authenticated users:

```csharp
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
        
        // Create claims for the token
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

        // Create token descriptor
        var now = DateTime.UtcNow;
        var expires = now.AddMinutes(Convert.ToDouble(_configuration["JwtConfig:ExpiryInMinutes"] ?? "60"));
        
        var tokenDescriptor = new SecurityTokenDescriptor
        {
            Subject = new ClaimsIdentity(claims),
            NotBefore = now,
            Expires = expires,
            SigningCredentials = new SigningCredentials(
                new SymmetricSecurityKey(key), SecurityAlgorithms.HmacSha256Signature),
            Issuer = _configuration["JwtConfig:Issuer"] ?? "DefaultIssuer",
            Audience = _configuration["JwtConfig:Audience"] ?? "DefaultAudience"
        };
        
        var token = jwtTokenHandler.CreateToken(tokenDescriptor);
        return jwtTokenHandler.WriteToken(token);
    }
}
```

## 4\. The Authentication Controller

Next, implement the controller that handles Google authentication:

```csharp
[Route("api/[controller]")]
[ApiController]
public class AuthController : ControllerBase
{
    private readonly UserManager<ApplicationUser> _userManager;
    private readonly RoleManager<IdentityRole> _roleManager;
    private readonly IConfiguration _configuration;
    private readonly TokenService _tokenService;

    public AuthController(
        UserManager<ApplicationUser> userManager,
        RoleManager<IdentityRole> roleManager,
        IConfiguration configuration,
        TokenService tokenService)
    {
        _userManager = userManager;
        _roleManager = roleManager;
        _configuration = configuration;
        _tokenService = tokenService;
    }

    [HttpPost("google-login")]
    public async Task<IActionResult> GoogleLogin([FromBody] GoogleLoginDTO model)
    {
        // Validate request
        if (string.IsNullOrEmpty(model.IdToken))
        {
            return BadRequest(new AuthResponseDto
            {
                IsSuccess = false,
                Message = "Google token is required"
            });
        }

        try
        {
            // Validate Google token
            var payload = await GoogleJsonWebSignature.ValidateAsync(
                model.IdToken,
                new GoogleJsonWebSignature.ValidationSettings
                {
                    Audience = new[] { _configuration["Google:ClientId"] }
                });

            // Check if user exists
            var user = await _userManager.FindByEmailAsync(payload.Email);
            if (user == null)
            {
                // Create new user if doesn't exist
                user = new ApplicationUser
                {
                    UserName = payload.Email,
                    Email = payload.Email,
                    EmailConfirmed = true,
                    SecurityStamp = Guid.NewGuid().ToString()
                };
                var createResult = await _userManager.CreateAsync(user);
                if (!createResult.Succeeded)
                {
                    return BadRequest(new AuthResponseDto
                    {
                        IsSuccess = false,
                        Message = "User creation failed: " +
                            string.Join(", ", createResult.Errors.Select(e => e.Description))
                    });
                }

                // Ensure Customer role exists
                if (!await _roleManager.RoleExistsAsync("Customer"))
                {
                    await _roleManager.CreateAsync(new IdentityRole("Customer"));
                }

                // Add to Customer role
                var roleResult = await _userManager.AddToRoleAsync(user, "Customer");
                if (!roleResult.Succeeded)
                {
                    return BadRequest(new AuthResponseDto
                    {
                        IsSuccess = false,
                        Message = "Failed to assign role: " +
                            string.Join(", ", roleResult.Errors.Select(e => e.Description))
                    });
                }
            }

            // Generate JWT token
            var token = await _tokenService.GenerateToken(user);
            var roles = await _userManager.GetRolesAsync(user);

            return Ok(new AuthResponseDto
            {
                IsSuccess = true,
                Token = token,
                UserName = user.UserName,
                Email = user.Email,
                Message = "Google login successful",
                Roles = roles.ToList()
            });
        }
        catch (Exception ex)
        {
            return BadRequest(new AuthResponseDto
            {
                IsSuccess = false,
                Message = $"Authentication failed: {ex.Message}"
            });
        }
    }
}
```

## 5\. Data Transfer Objects (DTOs)

Create the necessary DTOs for handling requests and responses:

```csharp
public class GoogleLoginDTO
{
    public string IdToken { get; set; }
}

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

## 6\. Frontend Implementation

Here's a simple HTML page with JavaScript to implement Google Sign-In:

Create a *wwwroot folder* and root and add *index.html*

```csharp
html<!DOCTYPE html>
<html>
<head>
    <title>Google OAuth</title>
    <script src="https://accounts.google.com/gsi/client" async defer></script>
</head>
<body>
    <h1>Login with Google</h1>
    <div id="g_id_onload"
         data-client_id="YOUR_GOOGLE_CLIENT_ID"
         data-callback="handleGoogleResponse"
         data-auto_prompt="false">
    </div>
    <div class="g_id_signin"
         data-type="standard"
         data-size="large"
         data-theme="outline"
         data-text="sign_in_with"
         data-shape="rectangular"
         data-logo_alignment="left">
    </div>
    <script>
        function handleGoogleResponse(response) {
            console.log("Google response received");
            // Send the ID token to your backend
            fetch('/api/auth/google-login', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                },
                body: JSON.stringify({ idToken: response.credential })
            })
            .then(response => response.json())
            .then(data => {
                console.log('Success:', data);
                if (data.isSuccess) {
                    // Store the JWT token
                    localStorage.setItem('jwt', data.token);
                    // Redirect or update UI as needed
                    alert('Login successful!');
                } else {
                    alert('Login failed: ' + data.message);
                }
            })
            .catch(error => {
                console.error('Error:', error);
                alert('Login failed');
            });
        }
    </script>
</body>
</html>
```