# Demo-SignalR

## Logs

At the end of this topic, you whould be able to:

- Explain what SignalR is and when it can be useful
- Identify, in general terms, how SignalR works "under the hood"
- Use the SignalR Hub as a high-level communication between clients and the server


## Resources

- [ASP.NET](https://www.asp.net/signalr) is a good starting point for learning about SignalR
  - [Introduction to SignalR](https://docs.microsoft.com/en-us/aspnet/signalr/overview/getting-started/introduction-to-signalr)
  - [Getting Started with SignalR2](https://docs.microsoft.com/en-us/aspnet/signalr/overview/getting-started/tutorial-getting-started-with-signalr)
  - [Server Broadcast with SignalR](https://docs.microsoft.com/en-us/aspnet/signalr/overview/getting-started/tutorial-server-broadcast-with-signalr)
  - [Real Time Web Applications with SignalR](https://docs.microsoft.com/en-us/aspnet/signalr/overview/getting-started/real-time-web-applications-with-signalr)
- [Bare-Bones Demo](https://github.com/dgilleland/Ad-Hoc-In-Class/tree/CPSC-1517/Bare%20Bones%20SignalR)

### Sample Code - Server-Side Hub

The Server Side hub is simply a class with methods that are "called" from the client via JavaScript.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using Microsoft.AspNet.SignalR;

namespace Website
{
    public class ChatHub : Hub
    {
        public void Send(string name, string message)
        {
            // Call the broadcastMessage method to update clients.
            Clients.All.broadcastMessage(name, message);
        }
    }
}
```

### Sample Code - Client-Side Hub Call

In the browser, there are elements that can be manipulated via JavaScript.

```html
<div class="container">
    <input type="text" id="message" />
    <input type="button" id="sendmessage" value="Send" />
    <input type="hidden" id="displayname" />
    <ul id="discussion"></ul>
</div>
```    

The JavaScript establishes the connections between the server and the client by

- Creating a JavaScript function that can be "called" by the server (setting the `.client.broadcastMessage`)
- Establish the connection with the server (calling `.start()` with a function in the `.done()` call that responds to the JavaScript promise)

```html
<script type="text/javascript">
        $(function () {
            // Declare a proxy to reference the hub.
            var chat = $.connection.chatHub;
            // Create a function that the hub can call to broadcast messages.
            chat.client.broadcastMessage = function (name, message) {
                // Html encode display name and message.
                var encodedName = $('<div />').text(name).html();
                var encodedMsg = $('<div />').text(message).html();
                // Add the message to the page.
                $('#discussion').append('<li><strong>' + encodedName
                    + '</strong>:&nbsp;&nbsp;' + encodedMsg + '</li>');
            };
            // Get the user name and store it to prepend to messages.
            $('#displayname').val(prompt('Enter your name:', ''));
            // Set initial focus to message input box.
            $('#message').focus();
            // Start the connection.
            $.connection.hub.start().done(function () {
                $('#sendmessage').click(function () {
                    // Call the Send method on the hub.
                    chat.server.send($('#displayname').val(), $('#message').val());
                    // Clear text box and reset focus for next comment.
                    $('#message').val('').focus();
                });
            });
        });
    </script>
```
