!SLIDE center

# Build basic chat application

!SLIDE smbullets

## General info

- Two apps to run
- Backend for the websocket and another app for the front end application
- Going to use Sinatra for the front end as we'll need something to play with authentication later.
- Quick and dirty (don't shoot me)

!SLIDE smbullets

## Why not all in Cramp?
- You probably can, I wanted to concentrate on how cramp does websockets in this talk.
- Also cause Cramp != easy views or templates

!SLIDE smbullets

## First - connect to websocket
- To get a 'welcome message from the server'

!SLIDE

## Gemfile

    @@@ Ruby
    source :rubygems
    
    # use latest cramp
    gem "cramp", :git => "http://github.com/indrekj/cramp.git"
    gem "rack"
    gem "thin"

    gem "sinatra"
    gem "haml"

    gem "yajl-ruby"

!SLIDE smbullets

## Backend (Cramp)

    @@@ Ruby
    # backend.rb
    require 'rubygems'
    require 'bundler'
    Bundler.setup

    require 'cramp'
    require 'yajl/json_gem'

    Cramp::Websocket.backend = :thin

    class Websocket < Cramp::Websocket
      on_start :user_connected

      def user_connected
        render ({:content => "Welcome to yact, chat away",
          :user => "yact"}).to_json
      end
    end

    Rack::Handler::Thin.run Websocket, :Port => 4000

!SLIDE
## Run the backend

    @@@ Cmd
    $ ruby backend.rb
    >> Thin web server (v1.2.7 codename No Hup)
    >> Maximum connections set to 1024
    >> Listening on 0.0.0.0:4000, CTRL+C to stop

!SLIDE
## Frontend (Sinatra)

    @@@ Ruby
    # frontend.rb
    require "rubygems"
    require "bundler"
    Bundler.setup

    require 'sinatra'
    require 'haml'

    get '/' do
      haml :index
    end

!SLIDE

## views/index.haml

    @@@ Haml
    %h2 YACT - Yet another chat thing
    .messages
    .post_message
      %form{:id => "send"}
        %input{:type => "text"}
        %br
        %input{:type => "submit", :value => "send"}

!SLIDE

## views/layout.haml

    @@@ Small
    !!!
    %html
      %head
        %title YACT
        %script{:type => "text/javascript",
          :src => "http://ajax.googleapis.com/ajax/libs/jquery/1.4.3/jquery.min.js"}
        %script{:type => "text/javascript", :src => "/javascripts/application.js"}
        %link{:href => "/stylesheets/style.css", :media => "screen",
          :rel => "stylesheet", :type => "text/css"}
      %body
        = yield

!SLIDE
## public/stylesheets/style.css

    @@@ Css
    /* Insert cool awesome css here */
    
    .messages {
      /* Make me look like a chat window */
    }
    ul.message {
      /* I'm a new message make me look pretty */
    }

!SLIDE

## public/javascripts/application.js (interesting bit)


    @@@ Javascript
    var Yact = {
      initialize: function() {
        this.connect();
      },

      connect: function() {
        this.socket = new WebSocket('ws://localhost:4000/');
        this.socket.onmessage = this.onmessage;
      },
      onmessage: function(m) {
        $(".messages").append(Yact.create_message(m.data));
      },
      create_message: function(message) {
        var data = JSON.parse(message);
        return "<ul class='message'><li class='from'><p>" + data["user"] +
          "</p></li><li class='content'><p>" + data["content"] +
          "</p></li></ul>";
      }
    }

    $(document).ready(function(){
      Yact.initialize();
    });

!SLIDE
## Run the frontend

    @@@ Cmd
    $ ruby frontend.rb
    == Sinatra/1.1.0 has taken the stage on 4567 for development with backup from Thin
    >> Thin web server (v1.2.7 codename No Hup)
    >> Maximum connections set to 1024
    >> Listening on 0.0.0.0:4567, CTRL+C to stop

!SLIDE center

    @@@ Internet
    http://localhost:4567/

!SLIDE

## should see basic connect and welcome message

!SLIDE smbullets
## Websocket API events

    @@@ Javascript
    this.socket.onopen = this.onopen;
    this.socket.onclose = this.onclose;
    this.socket.onerror = this.onerror;

- Read more on the javascript API:
- http://dev.w3.org/html5/websockets/