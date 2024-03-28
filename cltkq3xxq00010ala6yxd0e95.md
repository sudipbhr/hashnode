---
title: "Entity Framework Core - Code First Approach Example"
seoTitle: "Entity Framework Core - Code First Approach With Source Code"
seoDescription: "Entity Framework Core - Code First Approach With Source Code"
datePublished: Sat Mar 09 2024 23:35:11 GMT+0000 (Coordinated Universal Time)
cuid: cltkq3xxq00010ala6yxd0e95
slug: entity-framework-core-code-first-approach-example
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1710029459291/999dba9a-9ea8-4513-8a6b-8cdb4cdd0526.jpeg
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1710114291158/f24de7a2-8f56-4d68-a54b-694603c1c4ae.jpeg
tags: mvc, aspnet-core, code-first, codefirstapproach

---

. [ASP.NET](http://ASP.NET) Entity Framework Core simplifies data access in [ASP.NET](http://ASP.NET) applications by providing a powerful and flexible ORM (Object-Relational Mapping) framework with support for modern development practices and cross-platform compatibility.

To get started follow following steps:

1. Open Visual Studio and click on create a new project from right helper menu.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710024260511/49927289-47d7-4e4f-92e4-e3bf65f3e60a.png align="center")
    
    2. Choose ASP.NET Core Web App (Model-View-Controller)
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710024383168/ee05130b-d242-495b-a4e4-7c8f14ebed24.png align="center")
        
    3. Provide the appropriate name for the project and click on Next and click on create.
        
    4. Let's install the necessary packages now. Right-click on the dependencies from solution explorer and select Manage NuGet Packages
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710024799183/280acf71-c39c-4d9f-8532-a9cf7935fd25.png align="center")
        
    5. Install following packages
        
        1. Microsoft.EntityFrameworkCore.Design
            
            [`Microsoft.EntityFrameworkCore.Design`](http://Microsoft.EntityFrameworkCore.Design) contains all the design-time logic for Entity Framework Core. It's the code that all of the various tools (PMC cmdlets like `Add-Migration`, `dotnet ef` & `ef.exe`) call into.
            
        2. Microsoft.EntityFrameworkCore.Tools
            
            Enables these commonly used commands: Add-Migration Bundle-Migration Drop-Database Get-DbContext Get-Migration Optimize-DbContext Remove-Migration Scaffold-DbContext Script-Migration Update-Database
            
        3. Microsoft.EntityFrameworkCore.SqlServer
            
            Microsoft SQL Server database provider for Entity Framework Core.
            
    6. Now, define a new model class say `Student.cs` .Right click on Models and click on Add -&gt; Class. Provide appropriate name for class and click on Add.
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710025559542/8d3fd0d8-106b-459e-9697-ce54c7876da1.png align="center")
        
    7. Define Model class
        
        ```csharp
        using System.ComponentModel.DataAnnotations;
        using System.ComponentModel.DataAnnotations.Schema;
        using System.Security.Principal;
        
        namespace CodeFirstApproach.Models
        {
            public class Student
            {
                [Key]
                public int Id { get; set; }
        
                [Required]
                [Column("StudentName", TypeName ="varchar(100)")]
                public string Name { get; set; }
        
                [Required]
                [Column("StudentGender", TypeName = "varchar(100)")]
                public string Gender { get; set; }
        
                [Required]
                public int Age { get; set; }
        
            }
        }
        ```
        
    8. Define dbcontext
        
        Dbcontext is used to interact with the underlying database,manage database connection and is used to retrieve and save data in database. Instance of dbcontext represents session with the database which can be used to query and save instance of your entities to database.
        
        On models folder create another file StudentDBContext.cs
        
        ```csharp
        using Microsoft.EntityFrameworkCore; // Importing the necessary namespace
        
        namespace CodeFirstApproach.Models
        {
            public class StudentDBContext : DbContext // Defining a class named StudentDBContext which inherits from DbContext
            {
                // Constructor for the StudentDBContext class
                // It takes an argument of type DbContextOptions which typically carries information like connection string and other database configurations
                // It calls the base constructor of DbContext passing the options parameter to it
                public StudentDBContext(DbContextOptions options): base(options)
                {
                    // Constructor body
                    // It can contain initialization logic if needed
                }
        
                // DbSet property declaration
                // It declares a property named Students of type DbSet<Student>
                // DbSet<Student> represents the collection of all Student entities in the database
                // This property is used to interact with the Student table in the database
                public DbSet<Student> Students { get; set; }
            }
        }
        ```
        
    9. Now, it's time to connect to the database. Go to appsetting.json and add a connection string.
        
        ```json
        "ConnectionStrings": {
          "dbcs": "Data Source=(localdb)\\MSSQLLocalDB; Database=CodeFirst; Initial Catalog=CodeFirst;Integrated Security=True;Connect Timeout=30;Encrypt=False;Trust Server Certificate=False;Application Intent=ReadWrite;Multi Subnet Failover=False"
        }, //change initial catalog to set your database name
        ```
        
    10. Define your connection in Program.cs file as
        
        ```csharp
        var builder = WebApplication.CreateBuilder(args);
        
        // Add services to the container.
        builder.Services.AddControllersWithViews();
        
        var provider = builder.Services.BuildServiceProvider();
        var config = provider.GetRequiredService<IConfiguration>();
        builder.Services.AddDbContext<StudentDBContext>(item => item.UseSqlServer(config.GetConnectionString("dbcs")));
        
        var app = builder.Build();
        ```
        
        11. To migrate tables to the database. Goto Tools and select NuGet Package Manager and select NuGet Package Manager.
            
            On the package manager console, enter the commands:
            
            1. `add-migration` This command will create migrations
                
            2. `update-database` Reflects migrations on the database
                
        12. In HomeController.cs, create a constructor for HomeController as
            
            ```csharp
            using CodeFirstApproach.Models;
            using Microsoft.AspNetCore.Mvc;
            using System.Diagnostics;
            
            namespace CodeFirstApproach.Controllers
            {
                public class HomeController : Controller
                {
                    private readonly StudentDBContext studentDB;       
                    public HomeController(StudentDBContext studentDB)
                       {
                           this.studentDB = studentDB;
                       }
                }
            }
            ```
            
            This line defines a constructor for the `HomeController` class. Constructors are special methods that are called when an instance of a class is created. This constructor takes one parameter of type `StudentDBContext`, which means it expects an instance of `StudentDBContext` to be passed when an object of `HomeController` is instantiated.
            
        13. Now let's create a CRUD app for Student model class.
            
            1. Index Action
                
                Displays a list of students. Retrieves all students from the database using `studentDB.Students.ToList()` and passes them to the `Index` view.
                
                *Now, scaffold the code for Index method. Right click over the method name and click on Add View, Choose Razer View , List method for index, choose model class and dbcontext class and click on next.*
                
                ```csharp
                public IActionResult Index()
                {
                    var students = studentDB.Students.ToList();  
                    return View(students);
                }
                ```
                
            2. Create Action
                
                Displays a form for creating a new student. Returns the `Create` view which contains a form for entering student details.
                
                ```csharp
                public IActionResult Create()
                {
                    return View();
                }
                ```
                
            3. Post create action
                
                Handles the submission of the form to create a new student. Receives the form data submitted by the user, adds the new student to the database, and redirects to the `Index` action if the model state is valid. `ModelState.IsValid`: This property checks whether the model state is valid or not.
                
                ```csharp
                [HttpPost]
                public IActionResult Create(Student student)
                {
                    if (ModelState.IsValid)
                    {
                        studentDB.Students.Add(student);
                        studentDB.SaveChanges();
                        return RedirectToAction("Index");
                    }
                    return View(student);
                }
                ```
                
            4. Details action
                
                Displays details of specific student. `studentDB.Students.FirstOrDefault(s =>`[`s.Id`](http://s.Id)`== id);` retrieves the first `Student` entity from the database where the `Id` matches the provided `id`. If no such student is found, it returns `null`. The retrieved `Student` entity (or `null` if not found) is then stored in the variable `student`. This code is commonly used to retrieve a specific student from the database based on their ID.
                
                ```csharp
                public IActionResult Details(int id)
                {
                    var student = studentDB.Students.FirstOrDefault(s => s.Id == id);
                    return View(student);
                }
                ```
                
            5. Delete action
                
                Returns delete confirmation page with a record.
                
                ```csharp
                public IActionResult Delete(int id)
                {
                    var student = studentDB.Students.FirstOrDefault(s => s.Id == id);
                    return View(student);
                }
                ```
                
            6. Post delete action  
                Overloading delete method with post request.
                
                ```csharp
                [HttpPost, ActionName("Delete")]
                public IActionResult DeleteConfirmed(int id)
                {
                    var student = studentDB.Students.FirstOrDefault(s => s.Id == id);
                    studentDB.Students.Remove(student);
                    studentDB.SaveChanges();
                    return RedirectToAction("Index");
                }
                ```
                
            7. Edit action
                
                ```csharp
                public IActionResult Edit(int id)
                {
                    var student = studentDB.Students.Find(id);
                    return View(student);
                }
                ```
                
            8. Post Edit action
                
                ```csharp
                [HttpPost]
                public IActionResult Edit(int id, Student student)
                {
                    if(ModelState.IsValid)
                    {
                        studentDB.Students.Update(student);
                        studentDB.SaveChanges();
                        return RedirectToAction("Index");
                    }
                    return View(student);
                }
                ```
                
            
            Visit : [blog.1space.dev](https://blog.1space.dev)
            
            [Source Code](https://github.com/sudipbhr/ASP.NET-CodeFirst-Approach)