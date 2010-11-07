!SLIDE

# Extra stuff

!SLIDE

## Joining and leaving messages

    @@@ Ruby
    def user_connected
      ...
    end

    def user_disconnected
      ...
    end

!SLIDE

## Presence notifications

    @@@ Ruby
    periodic_timer :whos_online, :every => 2

    def whos_online
      User.connected.each do |c|
        c.render {:users_online => User.connected.keys}.to_json
      end
    end

Testing if the connection is up or not, re-connecting?
- You can implement such a thing checking the readyState of a websocket connection. When this readyState == 1 then connection is available.
- Do something like - While (connection_is_not_up)... Blah blah blah.


- To make it easier to know what's going on :

    @@@ Ruby
    Thin::Logging.trace = true

- will dump the HTTP requests and responses.

If you want presence bar on the side of the app or something:
You could use a periodic timer you can send the clients that are signed on to all users.
like this: