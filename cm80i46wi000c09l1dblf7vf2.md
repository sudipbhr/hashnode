---
title: "Building a RESTful API with ASP.NET Core and Data Annotations: A Comprehensive Guide"
seoTitle: "RESTful API Development with ASP.NET Core"
seoDescription: "Learn to build a RESTful API using ASP.NET Core, leveraging data annotations for structured and validated code"
datePublished: Sat Mar 08 2025 17:51:37 GMT+0000 (Coordinated Universal Time)
cuid: cm80i46wi000c09l1dblf7vf2
slug: building-a-restful-api-with-aspnet-core-and-data-annotations-a-comprehensive-guide
tags: apis, aspnet-core, aspnet-web-api, dotnet-aspnet-core-minimal-apis-route-parameters-restful-api-web-development

---

In today's web-driven world, RESTful APIs have become the backbone of modern applications, enabling seamless communication between different systems. [ASP.NET](http://ASP.NET) Core offers a powerful framework for building robust APIs, and when combined with data annotations, it provides an elegant solution for creating well-structured and validated APIs.

In this guide, we'll explore how to build a complete Book Records API using [ASP.NET](http://ASP.NET) Core and implement data validation using annotations. We'll cover everything from creating a new project to implementing CRUD operations with proper validation.

## Understanding ASP.NET Core Web API

ASP.NET Core Web API is a framework for building HTTP services that can be accessed from any client, including browsers and mobile devices. It's built on the ASP.NET Core platform and provides a more lightweight and modular approach compared to traditional ASP.NET.

Key features include:

* Cross-platform compatibility (Windows, macOS, Linux)
    
* High-performance architecture
    
* Built-in dependency injection
    
* Simplified routing using attributes
    
* Content negotiation and formatting
    
* Model binding and validation
    

## Data Annotations in .NET

Data annotations are attributes that you can apply to model classes and properties to:

* Enforce validation rules
    
* Specify display formats
    
* Define relationships between models
    
* Configure how model binding behaves
    

These annotations reside in the `System.ComponentModel.DataAnnotations` namespace and provide a declarative way to implement validation logic without writing extensive code.

Common annotations include:

* `[Required]`: Ensures a property has a value
    
* `[StringLength]`: Limits the length of a string
    
* `[Range]`: Specifies minimum and maximum values for numeric types
    
* `[EmailAddress]`: Validates email format
    
* `[RegularExpression]`: Validates against a regex pattern
    

## Step-by-Step Guide to Create a Book API

### 1\. Set Up Your Development Environment

First, ensure you have the following installed:

* [.NET 6.0 SDK](https://dotnet.microsoft.com/download) or later
    
* An IDE (Visual Studio, VS Code with C# extension, or JetBrains Rider)
    

### 2\. Create a New ASP.NET Core Web API Project

#### Using Visual Studio:

1. Open Visual Studio
    
2. Select "Create a new project"
    
3. Choose "ASP.NET Core Web API"
    
4. Name your project (e.g., "BookRecordAPI")
    
5. Select .NET 6.0 or later as the target framework
    
6. Uncheck "Use controllers" if you prefer minimal API style (for this guide, we'll use controllers)
    
7. Click "Create"
    

```xml
dotnet new webapi -n BookRecordAPI
cd BookRecordAPI
```

### 3\. Project Structure

After creating the project, you should have a folder structure similar to:

```xml
BookRecordAPI/
├── Controllers/
├── Properties/
├── appsettings.json
├── appsettings.Development.json
├── Program.cs
└── BookRecordAPI.csproj
```

### 4\. Create the Model Folder and Book Class

Create a new folder called "Models" and add a Book class:

```xml
using System.ComponentModel.DataAnnotations;

namespace BookRecordAPI.Models
{
    public class Book
    {
        public int Id { get; set; }
        
        [Required]
        [StringLength(200)]
        public string Title { get; set; }
        
        [Required]
        [StringLength(100)]
        public string Author { get; set; }
        
        [Required]
        [StringLength(50)]
        public string Genre { get; set; }
        
        [Range(1000, 2100, ErrorMessage = "Published Year must be between 1000 and 2100.")]
        public int PublishedYear { get; set; }
    }
}
```

## Implementing the Book Model with Annotations

Let's analyze each annotation in our Book model:

### 1\. \[Required\]

```xml
[Required]
public string Title { get; set; }
```

The `[Required]` attribute ensures that the property must have a value. For string properties, it verifies that the value isn't null or an empty string. When model validation occurs, [ASP.NET](http://ASP.NET) Core will automatically check this constraint.

### 2\. \[StringLength\]

```xml
[StringLength(200)]
public string Title { get; set; }
```

The `[StringLength]` attribute restricts the length of a string property. It accepts parameters for maximum length and optionally minimum length. In our example:

* Title has a maximum length of 200 characters
    
* Author has a maximum length of 100 characters
    
* Genre has a maximum length of 50 characters
    

### 3\. \[Range\]

```xml
[Range(1000, 2100, ErrorMessage = "Published Year must be between 1000 and 2100.")]
public int PublishedYear { get; set; }
```

The `[Range]` attribute specifies minimum and maximum values for numeric properties. In this case, we're ensuring that the PublishedYear is between 1000 and 2100.

The `ErrorMessage` parameter allows you to customize the validation error message that will be returned if the validation fails.

## Creating API Controllers and Routes

Now, let's create a controller to handle HTTP requests for our Book API:

```xml
using Microsoft.AspNetCore.Mvc;
using BookRecordAPI.Models;
using System.Collections.Generic;
using System.Linq;

namespace BookRecordAPI.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class BooksController : ControllerBase
    {
        private static List<Book> books = new List<Book>
        {
            new Book { Id = 1, Title = "1984", Author = "George Orwell", Genre = "Dystopian", PublishedYear = 1949 },
            new Book { Id = 2, Title = "To Kill a Mockingbird", Author = "Harper Lee", Genre = "Fiction", PublishedYear = 1960 }
        };

        // GET: api/books
        [HttpGet]
        public ActionResult<IEnumerable<Book>> GetBooks()
        {
            return Ok(books);
        }

        // GET: api/books/{id}
        [HttpGet("{id}")]
        public ActionResult<Book> GetBook(int id)
        {
            var book = books.FirstOrDefault(b => b.Id == id);
            if (book == null)
                return NotFound(new { message = "Book not found" });

            return Ok(book);
        }

        // POST: api/books
        [HttpPost]
        public ActionResult<Book> AddBook([FromBody] Book book)
        {
            if (books.Any(b => b.Id == book.Id))
                return BadRequest(new { message = "Book with this ID already exists" });

            books.Add(book);
            return CreatedAtAction(nameof(GetBook), new { id = book.Id }, book);
        }

        // PUT: api/books/{id}
        [HttpPut("{id}")]
        public ActionResult UpdateBook(int id, [FromBody] Book updatedBook)
        {
            var book = books.FirstOrDefault(b => b.Id == id);
            if (book == null)
                return NotFound(new { message = "Book not found" });

            book.Title = updatedBook.Title;
            book.Author = updatedBook.Author;
            book.Genre = updatedBook.Genre;
            book.PublishedYear = updatedBook.PublishedYear;

            return NoContent();
        }

        // PATCH: api/books/{id}
        [HttpPatch("{id}")]
        public ActionResult PatchBook(int id, [FromBody] Dictionary<string, object> updates)
        {
            var book = books.FirstOrDefault(b => b.Id == id);
            if (book == null)
                return NotFound(new { message = "Book not found" });

            foreach (var key in updates.Keys)
            {
                switch (key.ToLower())
                {
                    case "title":
                        book.Title = updates[key].ToString();
                        break;
                    case "author":
                        book.Author = updates[key].ToString();
                        break;
                    case "genre":
                        book.Genre = updates[key].ToString();
                        break;
                    case "publishedyear":
                        if (int.TryParse(updates[key].ToString(), out int year))
                            book.PublishedYear = year;
                        break;
                }
            }
            return NoContent();
        }

        // DELETE: api/books/{id}
        [HttpDelete("{id}")]
        public ActionResult DeleteBook(int id)
        {
            var book = books.FirstOrDefault(b => b.Id == id);
            if (book == null)
                return NotFound(new { message = "Book not found" });

            books.Remove(book);
            return NoContent();
        }
    }
}
```

### Key Controller Components Explained

#### 1\. Controller Attributes

```xml
[Route("api/[controller]")]
[ApiController]
```

* `[Route("api/[controller]")]`: Defines the route template for the controller. `[controller]` is replaced with the controller name (minus the "Controller" suffix), resulting in `/api/books`.
    
* `[ApiController]`: Adds API-specific behaviors, including automatic model validation and binding source parameter inference.
    

#### 2\. HTTP Method Attributes

```xml
[HttpGet]
[HttpGet("{id}")]
[HttpPost]
[HttpPut("{id}")]
[HttpPatch("{id}")]
[HttpDelete("{id}")]
```

These attributes map HTTP verbs to controller actions and define route templates. For example, `[HttpGet("{id}")]` maps HTTP GET requests to `/api/books/{id}` to the `GetBook` method.

#### 3\. Model Binding and Validation

```xml
public ActionResult<Book> AddBook([FromBody] Book book)
```

* `[FromBody]`: Indicates that the parameter should be bound from the request body.
    
* When a request comes in, [ASP.NET](http://ASP.NET) Core automatically:
    
    1. Deserializes the JSON request body to a Book object
        
    2. Validates the object against the data annotations
        
    3. Populates `ModelState` with any validation errors
        

#### 4\. Response Types

```xml
return Ok(books);
return NotFound(new { message = "Book not found" });
return BadRequest(new { message = "Book with this ID already exists" });
return CreatedAtAction(nameof(GetBook), new { id = book.Id }, book);
return NoContent();
```

These helper methods create appropriate HTTP responses with correct status codes:

* `Ok()`: 200 OK
    
* `NotFound()`: 404 Not Found
    
* `BadRequest()`: 400 Bad Request
    
* `CreatedAtAction()`: 201 Created with a Location header pointing to the new resource
    
* `NoContent()`: 204 No Content