---
title: "Connecting MYSQL Database to ASP.NET Project"
datePublished: Sun Mar 31 2024 02:54:32 GMT+0000 (Coordinated Universal Time)
cuid: cluexh7hv000008l9fcxu5t3s
slug: connecting-mysql-database-to-aspnet-project

---

1. Install `Pomelo.EntityFrameworkCore.MySql` from Nuget package manager console
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1711848620606/2f1de037-4571-4af8-883d-f8151db0ab8a.png align="center")
    
    2. Set the connection string in `appsettings.json` as
        
        ```json
        {
          "Logging": {
            "LogLevel": {
              "Default": "Information",
              "Microsoft.AspNetCore": "Warning"
            }
          },
          "AllowedHosts": "*",
          "ConnectionStrings": {
            "dbcs": "Server=localhost; Port=3306; Database=bulky; User=root; Password=;PersistSecurityInfo=True;"
          }
        }
        ```
        
    3. Change the `Program.cs` file as required to get Mysql connection string
        
        ```csharp
        
        var builder = WebApplication.CreateBuilder(args);
        
        // Add services to the container.
        builder.Services.AddControllersWithViews();
        
        // Register IConfiguration and read connection string from appsettings.json
        builder.Services.AddSingleton(builder.Configuration);
        
        // Configure DbContext with MySQL
        builder.Services.AddDbContext<ApplicationDbContext>((serviceProvider, options) =>
        {
            options.UseMySql(builder.Configuration.GetConnectionString("dbcs"),
                new MySqlServerVersion(new Version(8, 0, 28))); // Adjust the version according to your MySQL server version
        });
        
        var app = builder.Build();
        ```
        
    4. Now add-migration and update the database.