---
title: "Database First Approach Example - Entity Framework Core Framework"
datePublished: Tue Mar 12 2024 15:35:29 GMT+0000 (Coordinated Universal Time)
cuid: cltojaljq000209lb28cvg9bq
slug: database-first-approach-example-entity-framework-core-framework
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1710255357268/027b9e36-b941-4b37-8624-006d6fb78f03.png
tags: entity-framework, net, aspnet-core, databasefirstapproach

---

The Database First approach is suitable when the database schema is already defined or when there is an existing database that needs to be integrated into the application. It allows developers to focus on application logic without worrying about database design and SQL queries, as much of the database interaction is handled through the generated entity classes and ORM frameworks.

To get started with Database First Approach follow the following steps:

1. Open Visual Studio and click on create a new project from the right helper menu.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710024260511/49927289-47d7-4e4f-92e4-e3bf65f3e60a.png align="center")
    
    2. Choose ASP.NET Core Web App (Model-View-Controller)
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710024383168/ee05130b-d242-495b-a4e4-7c8f14ebed24.png align="left")
        
    3. Provide the appropriate name for the project and click on Next and click on Create.
        
    4. Let's install the necessary packages now. Right-click on the dependencies from Solution Explorer and select Manage NuGet Packages
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710024799183/280acf71-c39c-4d9f-8532-a9cf7935fd25.png align="center")
        
    5. Install following packages
        
    6. Microsoft.EntityFrameworkCore.Design
        
        [`Microsoft.EntityFrameworkCore.Design`](http://Microsoft.EntityFrameworkCore.Design) contains all the design-time logic for Entity Framework Core. It's the code that all of the various tools (PMC cmdlets like `Add-Migration`, `dotnet ef` & `ef.exe`) call into.
        
    7. Microsoft.EntityFrameworkCore.Tools
        
        Enables these commonly used commands: Add-Migration Bundle-Migration Drop-Database Get-DbContext Get-Migration Optimize-DbContext Remove-Migration Scaffold-DbContext Script-Migration Update-Database
        
    8. Microsoft.EntityFrameworkCore.SqlServer
        
        Microsoft SQL Server database provider for Entity Framework Core.
        
    9. Now let's create a models class and DB context using the scaffold command
        
        I already have a database on the server running and I am using the database that we have created on [Code First Approach](https://hashnode.com/post/cltkq3xxq00010ala6yxd0e95).
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710256399108/4d4a895d-302b-47ed-a58f-59518ee2a5ba.png align="center")
        
        7. Goto Tools and select NuGet Package Manager and select NuGet Package Manager.
            
            On the package manager console, enter the commands:
            
            ```csharp
            Scaffold-DbContext "Server=(localdb)\MSSQLLocalDB;database=CodeFirst4;trusted_connection=true" -Provider Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models
            ```
            
    
    *Note: If Scaffold-DbContext is not found error then install in the package manager console*
    
    ```csharp
    Install-Package Microsoft.EntityFrameworkCore.Tools
    ```
    
    8. Now you will see your model class and db context is generated for you from the database
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710257398774/19f28210-de73-48d6-b4f1-199171d0b123.png align="center")
        
        9. If you want to update the models class once the database schema has been changed. Run this command
            
            ```csharp
            Scaffold-DbContext "Server=(localdb)\MSSQLLocalDB;database=CodeFirst4;trusted_connection=true"Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models -force
            ```
            
        10. Now update your connection string in `appsettings.json` file and `Program.cs` file as:
            
            ```csharp
             "ConnectionStrings": {
               "dbcs": "Data Source=(localdb)\\MSSQLLocalDB; Database=CodeFirst; Initial Catalog=CodeFirst;Integrated Security=True;Connect Timeout=30;Encrypt=False;Trust Server Certificate=False;Application Intent=ReadWrite;Multi Subnet Failover=False"
             },
            ```
            
            ```csharp
            var builder = WebApplication.CreateBuilder(args);
            
            // Add services to the container.
            builder.Services.AddControllersWithViews();
            
            var provider = builder.Services.BuildServiceProvider();
            var config = provider.GetRequiredService<IConfiguration>();
            builder.Services.AddDbContext<StudentDBContext>(item => item.UseSqlServer(config.GetConnectionString("dbcs")));
            
            var app = builder.Build()
            ```
            
            To create the news continue with tutorials on the [code-first](https://hashnode.com/post/cltkq3xxq00010ala6yxd0e95) approach link
            
        11. Wait Wait, if you want to generate a controller and methods for the database there is a quicker way.
            
            1. Delete a previous controller eg: HomeController.cs
                
            2. Add a new controller to your controller folder and select MVC Controller with views using the Entity Framework option.
                
                ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710346824717/4197de6b-12af-4200-9003-7af417ad0f0b.png align="center")
                
            3. Choose a model class to generate views for and click on add as
                
                ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710346930010/a6d72e0b-7c0c-4b4d-a927-2f9b1ba3783a.png align="center")
                
            4. A scaffold will be generated for your model class, this might take some moments.
                
                ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710347026560/95b441f1-ed5e-44d5-a26d-9581340affe3.png align="center")
                
                As a result, all the views for students are updated.
                
            5. ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710348041358/15500e13-ea43-4f10-961d-cf1964a1a654.png align="center")