---
title: "ASP.NET Core MVC Login and Registration using Identity"
datePublished: Wed Mar 20 2024 16:44:41 GMT+0000 (Coordinated Universal Time)
cuid: clu01aewg00000ajrae7d7z8f
slug: aspnet-core-mvc-login-and-registration-using-identity
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1710978016361/920f7862-09ba-4521-a990-1463e7c95bde.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1710978008907/7b76bbc8-ef0d-4365-9ff8-3d0f555c98a4.png
tags: mvc, authentication, aspnet-core, login-and-register-using-aspnet

---

## What is identity in ASP.NET?

[ASP.NET](http://ASP.NET) Identity provides a robust and customizable framework for managing user authentication, authorization, and identity-related features in web applications. Some key features of [ASP.NET](http://ASP.NET) Identity include:

1. **User Authentication**:
    
2. **User Management**:
    
3. **Role-Based Authorization**:
    
4. **Claims-Based Identity**:
    
5. **Integration with**[**ASP.NET**](http://ASP.NET)**Frameworks**:
    

To start with the ASP.NET Identity model, Create a web app with Model, View, Controller

1. Go to nutget package manager and install the package named `Microsoft.VisualStudio.Web.CodeGeneration.Design`
    
    It is a code generation tool for [ASP.NET](http://ASP.NET) Core. Contains the dotnet-aspnet-codegenerator command used for generating controllers and views.
    
2. Now add identity to your project
    
    Add a scaffold item as:
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710951802243/7fcddbd8-b3fa-4090-b00c-d3ba3ebb84af.png align="center")
    
    Select Identity model and choose the desired scaffolds
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710951977196/2b68c9b2-8b75-4f8d-bdd3-ba548c8aec38.png align="center")
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710952057802/6c32c29e-f650-4a20-b0e7-38536e15a918.png align="center")
    
    include &lt;partial name="\_LoginPartial" /&gt; in the header section of \_Layout.cshtml and finally include app.MapRazorPages(); controller in Program.cs
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710974983805/e02279d2-d305-49b6-b321-09824dbbbdbd.png align="center")
    
3. Now run migration commands.
    
    1. add-migration
        
    2. update-database
        
4. Run the application and try registration and login
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710977125230/30a14274-7f04-44d2-83de-89d3ea9a929b.png align="center")
    
    5. Now expand the user identity model to add other details like first name , last name to registration page.
        

## Creating custom user model

1. Creating a custom model class file
    
    Create a modal class file for your custom user in Areas/Identity/Data/CustomUser.cs
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1711329934033/b192761b-b7d7-44fc-9824-deba50eca11b.png align="center")
    
2. Configure the entity mapping for a custom user entity named `CustomUser`. Update dbcontext file as required. The model class defines the method for Custom user model clas.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1711329995860/fad08dd7-777b-477b-8ff0-52e99e0ac79d.png align="center")
    
3. The custom user model class is ready for migration. Now, reflect the changes on the database by first creating a migration file and the updating database
    
    1. add-migration: to create a migration file
        
    2. update-database: to reflect the changes on the database.
        
4. Now update the register.cshtml file to include firstname and lastname for registration.
    
    ```csharp
    <div class="form-floating mb-3">
        <input asp-for="Input.FirstName" class="form-control" autocomplete="FirstName" aria-required="true" placeholder="FirstName.com" />
        <label asp-for="Input.FirstName">FirstName</label>
        <span asp-validation-for="Input.FirstName" class="text-danger"></span>
    </div>
    <div class="form-floating mb-3">
        <input asp-for="Input.LastName" class="form-control" autocomplete="LastName" aria-required="true" placeholder="LastName.com" />
        <label asp-for="Input.LastName">LastName</label>
        <span asp-validation-for="Input.LastName" class="text-danger"></span>
    </div>
    ```
    
5. Register.cshtml.cs must be updated with fields to be added on the Register.cshtml so include first name and last name as:
    
    ```csharp
      public class InputModel
      {
    
          [Required]
          [Display(Name = "First Name")]
          public string FirstName { get; set; }
    
          [Required]
          [Display(Name = "Last Name")]
          public string LastName { get; set; }
            
    ................
    ```
    
6. Build the application to get new registration page
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1711331195520/232ef5d3-abf9-4ff1-af5f-d9d8acf39798.png align="center")