---
title: "Role-Based Authentication in ASP.NET"
datePublished: Mon Apr 15 2024 00:27:59 GMT+0000 (Coordinated Universal Time)
cuid: clv07uiya000408l49htu0al2
slug: role-based-authentication-in-aspnet
tags: authentication, aspnet-core

---

1. **Use** `AddRoles<IdentityRole>` **to add role management capabilities.**
    
    ```csharp
    builder.Services.AddDefaultIdentity<IdentityUser>(options => options.SignIn.RequireConfirmedAccount = true)
        .AddRoles<IdentityRole>()
        .AddEntityFrameworkStores<AuthenticationContext>();
    ```
    
2. **Create Roles if Not Exist**:
    
    include it before app.Run();
    
    ```csharp
    using(var scope = app.Services.CreateScope())
    {
        var roleManager = scope.ServiceProvider.GetRequiredService<RoleManager<IdentityRole>>();
    
        var roles = new[] { "Admin", "Manaager" };
        foreach(var role in roles)
        {
            if (!await roleManager.RoleExistsAsync(role))
                await roleManager.CreateAsync(new IdentityRole(role));
        }
    }
    ```
    
3. create a default admin user
    
    ```csharp
    using (var scope = app.Services.CreateScope())
    {
        var userManager = scope.ServiceProvider.GetRequiredService<UserManager<IdentityUser>>();
    
        string email = "admin@admin.com";
        string password = "Test@1234";
        if(await userManager.FindByEmailAsync(email) == null)
        {
            var user = new IdentityUser();
            user.UserName = email;
            user.Email = email;
            user.EmailConfirmed = true;
            var result = await userManager.CreateAsync(user,password);
            if (result.Succeeded)
            {
                await userManager.AddToRoleAsync(user, "Admin");
            }
            else
            {
                foreach (var error in result.Errors)
                {
                    // Log or handle each error
                    Console.WriteLine(error.Description);
                }
            }
            
        }
    
    }
    ```
    
4. Adding authorization attributes on controller class or action methods
    
    ```csharp
     [Authorize(Roles = "Member")]
     public class StudentController : Controller
     {
         private readonly AuthenticationContext authenticateDb;
    
         public StudentController(AuthenticationContext authenticateDb)
         {
             this.authenticateDb = authenticateDb;
    
         }
    
       }
    ```
    
5. We can also check the user's role in the templates and show the page according to the role
    
    ```csharp
    @using Microsoft.AspNetCore.Identity
    @inject SignInManager<IdentityUser> SignInManager
    @inject UserManager<IdentityUser> UserManager
    
    @if (User.IsInRole("Admin"))
    {
        <h1> Hi Admin</h1>
    }
    
    
    @if (User.Identity.IsAuthenticated)
    {
        <h1> Hi @User.Identity.Name</h1>
        <p>Your role(s): @string.Join(", ", await UserManager.GetRolesAsync(await UserManager.FindByNameAsync(User.Identity.Name)))</p>
    }
    ```