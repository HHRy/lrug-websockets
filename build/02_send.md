!SLIDE

## Next - send a message

!SLIDE smbullets

## Backend (websocket.rb)

    @@@ Ruby
    class User
      def self.connected
        @connected ||= []
      end
    end

    class Websocket < Cramp::Websocket
      on_start :user_connected
      on_finish :user_disconnected


      def user_connected
        render ({:content => "Welcome to yact, chat away",
          :user => "yact"}).to_json
        # record all chat users connected
        User.connected << self
      end

      def user_disconnected
        # remove chat user from connection
        User.connected.delete self
      end
    end

!SLIDE

    @@@ Ruby
    class Websocket < Cramp::Websocket
      ...
      on_data :message_received

      def message_received(data)
        # send message to all connected users
        User.connected.each do |c|
          c.render data
        end
      end
    end

!SLIDE

## public/javascripts/application.js

    @@@ Javascript
    var Yact = {
      ...
      send: function(data) {
        setTimeout(function(){
          Yact.socket.send(JSON.stringify(data));
        },100);
      }
    }

    $(document).ready(function(){
      ...
      $("form#send").submit(function(){
        Yact.send({"content":$("input[type='text']").val(), "user":"joe"});
        return false;
      });
    });

!SLIDE smbullets

## Reload backend and frontend

- Done - You will be able to send a message
- Other connected users will see the message