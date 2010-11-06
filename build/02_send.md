!SLIDE

## Next - send a message

!SLIDE
So javascript document.ready( this.socket = ws://host:port/chat )

onmessage for the welcome message simply push into a chat window.
console.log(message)
// simplify the javascript so that it's smaller for presentation, so no classes, etc.

Next sending a chat message
- send text through the socket.
(JAVASCRIPT)

Then in crap receive_data receive_message
- Need to send it to all connected clients, so have to modify on_connect to make sure we know who is connected:
- clients << self
And also when people leave
clients.delete(self)
- Then iterate over those clients to render the received_message.

Improve it a little.
- Define a simple structure for a message, in this example, from, when, content,
- Will be sent to the server in JSON.
- (show the code)

- Therefore update the server for on_connect to be structured too.