---
title: "How to Create a WebSocket in ASP.NET Using .NET 8"
datePublished: Fri May 09 2025 02:07:09 GMT+0000 (Coordinated Universal Time)
cuid: cmag5oerc000309le4e3hbeux
slug: how-to-create-a-websocket-in-aspnet-using-net-8
tags: web-sockets, aspnet, socket

---

## Add WebSocket Middleware

In `.NET 8`, middleware configuration can stay outside `Program.cs` if you organize it into dedicated classes or handlers.

Let’s write a **WebSocketHandler** class.

### 📄 `WebSocketHandler.cs`

```csharp
using System.Net.WebSockets;
using System.Text;

namespace WebSocketDemo
{
    public class WebSocketHandler
    {
        public async Task HandleAsync(WebSocket webSocket, CancellationToken cancellationToken)
        {
            Console.WriteLine("WebSocket connection established");

            var sendTask = SendPeriodicMessagesAsync(webSocket, cancellationToken);
            var receiveTask = ReceiveMessagesAsync(webSocket, cancellationToken);

            await Task.WhenAny(sendTask, receiveTask);
        }

        private async Task SendPeriodicMessagesAsync(WebSocket webSocket, CancellationToken cancellationToken)
        {
            while (!cancellationToken.IsCancellationRequested && webSocket.State == WebSocketState.Open)
            {
                var message = "Hello";
                var buffer = Encoding.UTF8.GetBytes(message);

                await webSocket.SendAsync(
                    new ArraySegment<byte>(buffer),
                    WebSocketMessageType.Text,
                    true,
                    cancellationToken
                );

                Console.WriteLine($"Sent: {message}");

                try
                {
                    await Task.Delay(TimeSpan.FromMinutes(1), cancellationToken);
                }
                catch (TaskCanceledException)
                {
                    break;
                }
            }
        }

        private async Task ReceiveMessagesAsync(WebSocket webSocket, CancellationToken cancellationToken)
        {
            var buffer = new byte[1024 * 4];

            while (!cancellationToken.IsCancellationRequested && webSocket.State == WebSocketState.Open)
            {
                var result = await webSocket.ReceiveAsync(new ArraySegment<byte>(buffer), cancellationToken);

                if (result.MessageType == WebSocketMessageType.Close)
                {
                    Console.WriteLine("Client requested close");
                    await webSocket.CloseAsync(WebSocketCloseStatus.NormalClosure, "Closing", cancellationToken);
                    break;
                }
            }
        }
    }
}
```

Configure WebSockets in Program.cs

```csharp
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

app.UseWebSockets();

var webSocketHandler = new WebSocketHandler();

app.Map("/ws", async context =>
{
    if (context.WebSockets.IsWebSocketRequest)
    {
        var webSocket = await context.WebSockets.AcceptWebSocketAsync();
        await webSocketHandler.HandleAsync(webSocket, context.RequestAborted);
    }
    else
    {
        context.Response.StatusCode = 400;
    }
});

app.Run();
```

  
Create a Frontend to Connect  
Let’s create a simple HTML file

```csharp
<!DOCTYPE html>
<html>
<head>
    <title>WebSocket Client</title>
    <style>
        #socket_box {
            width: 400px;
            height: 300px;
            border: 1px solid #ccc;
            padding: 10px;
            overflow-y: auto;
            font-family: sans-serif;
        }
    </style>
</head>
<body>
    <h1>WebSocket Messages</h1>
    <div id="socket_box"></div>

    <script>
        const ws = new WebSocket('wss://localhost:5001/ws');

        ws.onopen = () => {
            console.log('WebSocket connected');
        };

        ws.onmessage = (event) => {
            const box = document.getElementById('socket_box');
            const msg = document.createElement('div');
            msg.textContent = event.data + ' at ' + new Date().toLocaleTimeString();
            box.appendChild(msg);
            box.scrollTop = box.scrollHeight;
        };

        ws.onclose = () => {
            console.log('WebSocket closed');
        };
    </script>
</body>
</html>
```