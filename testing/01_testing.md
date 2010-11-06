!SLIDE

## A bit on Testing

!SLIDE

- Show simple ability to bring up server, send a message via websocket library and test receiving of json

That's all great and well but how can that be tested. The way i've tested this stuff so far is basically by running up a mini-ws server and then testing against that using a ruby websocket library.
- Show how to test posting a message and then making sure the clients receive a message.
- Show how to bring up and take down a server (quick and simple on different port)
- Then implement the ability to post a message

Show how best to send messages to all clients from the cramp websocket controller.