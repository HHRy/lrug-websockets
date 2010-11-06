## Extra stuff
Testing if the connection is up or not, re-connecting?
- You can implement such a thing checking the readyState of a websocket connection. When this readyState == 1 then connection is available.
- Do something like - While (connection_is_not_up)... Blah blah blah.

Join and leave meessages
- Client's names joining and leaving the room.

- To make it easier to know what's going on :

    @@@ Ruby
    Thin::Logging.trace = true

- will dump the HTTP requests and responses.

If you want presence bar on the side of the app or something:
You could use a periodic timer you can send the clients that are signed on to all users.
like this: