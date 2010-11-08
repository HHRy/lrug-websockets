!SLIDE smbullets

# Cramp
- Created by Pratik - @lifo
- An asynchronous framework
- EventMachine loop
- Websockets support, woo hoo!

!SLIDE
    @@@ Ruby
    # example from repo
    require "rubygems"
    require "bundler"
    Bundler.setup

    require 'cramp'
    require 'thin'

    class WelcomeController < Cramp::Action
      on_start :say_hello
      def say_hello
        render 'Hello World'
        finish
      end
    end

    Rack::Handler::Thin.run WelcomeController, :Port => 3000

!SLIDE smbullets

## Thin / Rainbows

!SLIDE

## Route to different actions

    @@@ Ruby
    routes = HttpRouter.new do
      add('/(:password)').to(HomeController)
    end

    Rack::Handler::Thin.run routes, :Port => 3000

!SLIDE
## Websocket action

    @@@ Ruby
    # set the webscoket backend
    Cramp::Websocket.backend = :thin

    class WelcomeController < Cramp::Websocket
      on_start :welcome
      on_data :received_data

      def received_data(data)
        render "Got your #{data}"
      end

      def welcome
        render "Welcome to websocket"
      end
    end

    Rack::Handler::Thin.run WelcomeController, :Port => 3000

!SLIDE smbullets
## Other hooks
- `periodic_timer` will run something ever x secs
- There are other hooks you can use like `before_start` `on_start`

      @@@ Ruby
      periodic_timer :say_hello, :every => 2
      def say_hello
        render "hello you're still online"
      end

!SLIDE smbullets

## Ensure using latest Cramp

- Master `github.com/lifo/cramp` websocket handshake broken:

    `Error during WebSocket handshake:
    'sec-websocket-origin' header is missing`
    
- Fixed by `github.com/indrekj/cramp`