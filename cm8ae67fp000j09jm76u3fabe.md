---
title: "Understanding Dependency Injection in ASP.NET with Real-World Examples"
seoTitle: "ASP.NET Dependency Injection Guide with Examples"
seoDescription: "Learn Dependency Injection in ASP.NET Core by building an Order Processing System with real-world examples of logging, emailing, and payment services"
datePublished: Sat Mar 15 2025 15:58:54 GMT+0000 (Coordinated Universal Time)
cuid: cm8ae67fp000j09jm76u3fabe
slug: understanding-dependency-injection-in-aspnet-with-real-world-examples

---

Here is real life example of Dependency Injection (DI) in an [ASP.NET](http://ASP.NET) Core application. We'll build a simple **Order Processing System** where the system depends on various services like logging, emailing, and payment processing.

### Scenario:

We have a system that processes orders. When an order is placed:

1. A **Payment** needs to be processed.
    
2. A **Confirmation Email** should be sent to the customer.
    
3. A **Logging** service logs each step of the order process.
    

We want to keep these services decoupled so we can easily swap them out or mock them for testing. This is where **Dependency Injection (DI)** comes into play.

### 1. **Define Interfaces and Implementations**

We'll start by defining the services that our order processing system will depend on.

#### Payment Service

```xml
public interface IPaymentService
{
    bool ProcessPayment(Order order);
}

public class PaymentService : IPaymentService
{
    public bool ProcessPayment(Order order)
    {
        // Imagine calling an external payment provider API here.
        Console.WriteLine($"Processing payment for Order ID: {order.OrderId}");
        return true;  // Payment success
    }
}
```

#### Email Service

```xml
public interface IEmailService
{
    void SendOrderConfirmationEmail(Order order);
}

public class EmailService : IEmailService
{
    public void SendOrderConfirmationEmail(Order order)
    {
        // Imagine sending an email here
        Console.WriteLine($"Sending confirmation email to {order.CustomerEmail} for Order ID: {order.OrderId}");
    }
}
```

#### Logging Service

```xml
public interface ILoggingService
{
    void Log(string message);
}

public class LoggingService : ILoggingService
{
    public void Log(string message)
    {
        // Imagine writing logs to a file, database, or external service.
        Console.WriteLine($"Log: {message}");
    }
}
```

### 2. **Create the Order Model**

We'll create a simple `Order` model to pass data through the services.

```xml
public class Order
{
    public int OrderId { get; set; }
    public string CustomerEmail { get; set; }
    public decimal TotalAmount { get; set; }
}
```

### 3. **Define the Order Processor**

This is the main service that coordinates the different operations (payment, email, logging) when an order is placed.

```xml
public class OrderProcessor
{
    private readonly IPaymentService _paymentService;
    private readonly IEmailService _emailService;
    private readonly ILoggingService _loggingService;

    // Constructor Injection
    public OrderProcessor(
        IPaymentService paymentService,
        IEmailService emailService,
        ILoggingService loggingService)
    {
        _paymentService = paymentService;
        _emailService = emailService;
        _loggingService = loggingService;
    }

    public void ProcessOrder(Order order)
    {
        // Log the order processing
        _loggingService.Log($"Started processing Order ID: {order.OrderId}");

        // Process payment
        var paymentSuccess = _paymentService.ProcessPayment(order);
        if (paymentSuccess)
        {
            _loggingService.Log($"Payment successful for Order ID: {order.OrderId}");

            // Send email confirmation
            _emailService.SendOrderConfirmationEmail(order);
            _loggingService.Log($"Confirmation email sent for Order ID: {order.OrderId}");
        }
        else
        {
            _loggingService.Log($"Payment failed for Order ID: {order.OrderId}");
        }

        // Final log
        _loggingService.Log($"Finished processing Order ID: {order.OrderId}");
    }
}
```

### 4. **Register Services in the DI Container**

Now, we need to register our services (`PaymentService`, `EmailService`, `LoggingService`) in the DI container so that they can be injected into the `OrderProcessor`.

#### In `Startup.cs` (for [ASP.NET](http://ASP.NET) Core 3.x or .NET 5):

```xml
public void ConfigureServices(IServiceCollection services)
{
    // Register services with DI container
    services.AddTransient<IPaymentService, PaymentService>();  // Transient - new instance each time
    services.AddTransient<IEmailService, EmailService>();      // Transient - new instance each time
    services.AddTransient<ILoggingService, LoggingService>();  // Transient - new instance each time

    // Register OrderProcessor service
    services.AddTransient<OrderProcessor>();

    services.AddControllers();
}
```

#### In `Program.cs` (for [ASP.NET](http://ASP.NET) Core 6+):

```xml
var builder = WebApplication.CreateBuilder(args);

// Register services with DI container
builder.Services.AddTransient<IPaymentService, PaymentService>();
builder.Services.AddTransient<IEmailService, EmailService>();
builder.Services.AddTransient<ILoggingService, LoggingService>();
builder.Services.AddTransient<OrderProcessor>();

builder.Services.AddControllers();

var app = builder.Build();
app.MapControllers();
app.Run();
```

### 5. **Inject** `OrderProcessor` into a Controller

Now, let's use **constructor injection** to inject the `OrderProcessor` into a controller where we handle HTTP requests.

```xml
[ApiController]
[Route("api/[controller]")]
public class OrdersController : ControllerBase
{
    private readonly OrderProcessor _orderProcessor;

    // Constructor Injection
    public OrdersController(OrderProcessor orderProcessor)
    {
        _orderProcessor = orderProcessor;
    }

    [HttpPost]
    [Route("process")]
    public IActionResult ProcessOrder([FromBody] Order order)
    {
        // Use OrderProcessor to handle the order
        _orderProcessor.ProcessOrder(order);
        
        return Ok("Order processed successfully!");
    }
}
```

### 6\. **Example Request**

Now, when you make a POST request to the API endpoint `/api/orders/process` with an order body:

```xml
{
  "OrderId": 1,
  "CustomerEmail": "customer@example.com",
  "TotalAmount": 99.99
}
```

The system will:

1. Log the order processing.
    
2. Process the payment.
    
3. Send a confirmation email.
    
4. Log the result at each step.
    

### 7\. **How DI Works in This Example**

* The `OrderProcessor` depends on three services: `IPaymentService`, `IEmailService`, and `ILoggingService`.
    
* These dependencies are injected by [ASP.NET](http://ASP.NET) Core's **DI container** via constructor injection.
    
* The **DI container** automatically resolves and provides the appropriate instances of `PaymentService`, `EmailService`, and `LoggingService` when the `OrderProcessor` is instantiated.
    
* This means we can easily swap out the implementations of these services without modifying the `OrderProcessor` class. For example, we could replace `PaymentService` with a mock version for testing or swap in a new payment provider.
    

### 8\. **Benefits of Dependency Injection**

1. **Loose Coupling:** The `OrderProcessor` is not tightly coupled to specific implementations of `PaymentService`, `EmailService`, or `LoggingService`. You can swap them out as needed.
    
2. **Testability:** It’s easy to mock or replace services in tests. For example, in unit tests, you can mock `IPaymentService` and `IEmailService`.
    
3. **Maintainability:** Adding new features or services doesn’t require changes to the core logic (e.g., adding a new `ShippingService`).
    
4. **Configuration Management:** You can configure services in one central location (DI container), which makes managing complex dependencies easier.
    

### 9\. **Conclusion**

This real-life example of DI in [ASP.NET](http://ASP.NET) Core demonstrates how you can build a loosely-coupled, maintainable, and testable application. By injecting dependencies like `PaymentService`, `EmailService`, and `LoggingService` into the `OrderProcessor`, you ensure that the application is flexible and scalable, while also promoting good software engineering practices like separation of concerns and single responsibility.