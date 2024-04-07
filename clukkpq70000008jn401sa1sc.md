---
title: "Sending email in asp.net core MVC"
datePublished: Thu Apr 04 2024 01:43:52 GMT+0000 (Coordinated Universal Time)
cuid: clukkpq70000008jn401sa1sc
slug: sending-email-in-aspnet-core-mvc
tags: mail, aspnet-mail, send-email-aspnet

---

1. Create a view with a form:
    

Create a view file (e.g., `SendEmail.cshtml`) where you'll place your form. You can place this view file in the `Views` folder, inside a folder corresponding to your controller (e.g., `Views/Email`). Here's a basic example of what your view might look like:

```csharp
htmlCopy code@{
    ViewData["Title"] = "Send Email";
}

<h1>Send Email</h1>

<form asp-controller="Email" asp-action="SendEmail" method="post">
    <div class="form-group">
        <label for="toEmail">To Email:</label>
        <input type="email" class="form-control" id="toEmail" name="toEmail" required>
    </div>
    <div class="form-group">
        <label for="subject">Subject:</label>
        <input type="text" class="form-control" id="subject" name="subject" required>
    </div>
    <div class="form-group">
        <label for="body">Body:</label>
        <textarea class="form-control" id="body" name="body" required></textarea>
    </div>
    <button type="submit" class="btn btn-primary">Send Email</button>
</form>
```

2. Install MailKit package on Nuget package manager console.
    
    `PM> Install-Package MailKit`
    
3. Create a controller action to handle the form submission:
    

In your controller (e.g., `EmailController`), create an action method to handle the form submission. Here's an example:

```csharp
using System.Net.Mail; 
using System.Net; 
using MailKit; 
using MailKit.Net.Smtp;
using Microsoft.AspNetCore.Mvc;
using System.Net;
using System.Net.Mail;
using System.Threading.Tasks;

public class EmailController : Controller
{
    public IActionResult SendEmail()
    {
        return View();
    }

    [HttpPost]
    public IActionResult SendEmail(string toEmail, string subject, string body)
         {
             if (string.IsNullOrEmpty(toEmail) || string.IsNullOrEmpty(subject) || string.IsNullOrEmpty(body))
             {
                 ModelState.AddModelError("", "Please provide all required fields.");
                 return View(); // Consider returning appropriate view here.
             }

             try
             {
                 var email = new MimeMessage();
                 email.From.Add(new MailboxAddress("Sender Name", "sudipbhandari67@gmail.com"));
                 email.To.Add(new MailboxAddress("Receiver Name", toEmail));
                 email.Subject = subject;
                 email.Body = new TextPart("plain")
                 {
                     Text = body
                 };

                 using (var smtp = new MailKit.Net.Smtp.SmtpClient())
                 {
                     smtp.Connect("smtp.gmail.com", 587, false);
                     smtp.Authenticate("sender's_email@gmail.com", "third party app password");
                     smtp.Send(email);
                     smtp.Disconnect(true);
                 }

                 return View("SendEmail"); // Return success view
             }
             catch (Exception ex)
             {
                 Console.WriteLine(ex.ToString());
                 ModelState.AddModelError("", $"Failed to send email: {ex.Message}");
                 return View(); // Consider returning appropriate view here.
             }

         }
     }
 }

}
```

Replace `"sender's_email@gmail.com"` and `"third party app password"` with your Gmail email address and password respectively. In case of gmail you need to create app passwords

With these steps, you've created a view with a form for sending emails and a controller action to handle the form submission. When the form is submitted, the data is passed directly to the `SendEmail` action, which sends the email using Gmail's SMTP server.