!SLIDE

# Extra stuff

!SLIDE

## Joining and leaving messages

    @@@ Ruby
    class User
      def self.connected
        @connected ||= {}
      end
    end

    class Websocket < Websocket::Cramp
      ...
      def user_connected
        User.connected[current_user] = self
        User.connected.each do |u, c|
          c.render {:content => "#{current_user} joined the chat"}.to_json
        end
      end

      def user_disconnected
        User.connected.delete current_user
        User.connected.each do |u, c|
          c.render {:content => "#{current_user} left the chat"}.to_json
        end
      end
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

!SLIDE smbullets

## Frontend testing for a connection

    @@@ Javascript

    this.socket.readyState != 1

- Websocket API defines a `readyState` for the connection status
- set this up in a javascript interval and then clear on successful connection


!SLIDE smbullets
## debugging http

    @@@ Ruby
    # add to websocket.rb
    Thin::Logging.trace = true

- will dump the HTTP requests and responses for handshake

!SLIDE smbullets

## Other browsers - internet explorer

- A library that uses flash to make the websocket connection
- `https://github.com/gimite/web-socket-js`
- socket.io - cross browser websocket javascript lib
- Haven't used either yet cause I don't care