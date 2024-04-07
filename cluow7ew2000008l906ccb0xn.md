---
title: "Sending Emails In ASP.NET Core"
seoDescription: "sending emails using asp.net core"
datePublished: Sun Apr 07 2024 02:16:37 GMT+0000 (Coordinated Universal Time)
cuid: cluow7ew2000008l906ccb0xn
slug: sending-emails-in-aspnet-core
tags: email, aspnet-core, gmail, send-email-in-aspnet

---

1. ```csharp
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
    ```
    
    Note: include the following necessary packages
    

```csharp
using System.Net.Mail; 
using System.Net; 
using MailKit; 
using MailKit.Net.Smtp;
```