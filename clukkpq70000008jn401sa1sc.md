---
title: "Sending email in asp.net core MVC"
datePublished: Thu Apr 04 2024 01:43:52 GMT+0000 (Coordinated Universal Time)
cuid: clukkpq70000008jn401sa1sc
slug: sending-email-in-aspnet-core-mvc

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

2. Create a controller action to handle the form submission:
    

In your controller (e.g., `EmailController`), create an action method to handle the form submission. Here's an example:

```csharp
csharpCopy codeusing Microsoft.AspNetCore.Mvc;
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
    public async Task<IActionResult> SendEmail(string toEmail, string subject, string body)
    {
        if (string.IsNullOrEmpty(toEmail) || string.IsNullOrEmpty(subject) || string.IsNullOrEmpty(body))
        {
            ModelState.AddModelError("", "Please provide all required fields.");
            return View();
        }

        try
        {
            var smtpClient = new SmtpClient
            {
                Host = "smtp.gmail.com",
                Port = 587,
                EnableSsl = true,
                Credentials = new NetworkCredential("your_email@gmail.com", "your_password")
            };

            using (var message = new MailMessage("your_email@gmail.com", toEmail))
            {
                message.Subject = subject;
                message.Body = body;
                message.IsBodyHtml = true;

                await smtpClient.SendMailAsync(message);
            }

            return RedirectToAction("EmailSent");
        }
        catch (Exception ex)
        {
            ModelState.AddModelError("", $"Failed to send email: {ex.Message}");
            return View();
        }
    }

    public IActionResult EmailSent()
    {
        return View();
    }
}
```

Replace `"`[`your_email@gmail.com`](mailto:your_email@gmail.com)`"` and `"your_password"` with your Gmail email address and password respectively.

With these steps, you've created a view with a form for sending emails and a controller action to handle the form submission. When the form is submitted, the data is passed directly to the `SendEmail` action, which sends the email using Gmail's SMTP server.