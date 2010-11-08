!SLIDE

# Authentication

!SLIDE smbullets

## Authentication
- Websocket Spec outlines that cookies to be sent on websocket connection handshake
- Backend can use this cookie to authenticate websocket connection
- Frontend can set cookie
- Cramp and Sinatra are rack - create some middleware

!SLIDE

## Temp user - user.rb

    @@@ Ruby
    class User
      def self.temp_user
        require 'ostruct'
        @temp_user ||= OpenStruct.new(:token => "123",
          :username => "kalv", :password => "password")
      end

      def self.authenticate(username, password)
        temp_user if temp_user.username == username && temp_user.password == password
      end

      def self.authenticate_by_token(token)
        temp_user if temp_user.token == token
      end
    end

!SLIDE

## Rack middleware - auth.rb

    @@@ Ruby
    require 'user'

    class Auth
      def initialize(app)
        @app = app
      end

      def call(env)
        env['user'] = authenticate(env)
        @app.call(env)
      end

      module Helper
        def current_user
          request.env['user']
        end
      end

      private

      def authenticate(env)
        request = Rack::Request.new(env)
        token = request.cookies["token"]
        current_user = User.authenticate_by_token(token)
        current_user ? current_user : nil
      end
    end

!SLIDE

## frontend.rb

    @@@ Ruby
    ...
    require 'auth'
    use Auth # enable auth middleware
    include Auth::Helper # for current_user

    get '/' do
      redirect '/login' unless current_user
      haml :index
    end

    get '/login' do
      haml :login
    end

    post '/login' do
      if user = User.authenticate(params[:username], params[:password])
        response.set_cookie("token", user.token)
        redirect '/'
      else # not authenticated
        redirect '/login'
      end
    end

!SLIDE

## backend.rb

    @@@ Ruby
    require 'auth'
    include Auth::Helper

    class Websocket < Cramp::Websocket
      ...
      def user_connected
        if current_user
          render ({:content => "Welcome to yact, chat away",
            :user => "yact"}).to_json
          User.connected << self
        else
          self.render({:content => "You are unauthenticated to chat here",
            :user => "yact"}.to_json)
          finish
        end
      end

      def message_received(data)
        m = JSON.parse(data).merge(:user => current_user.username)
        User.connected.each do |c|
          c.render m.to_json
        end
      end
    end

    Rack::Handler::Thin.run Auth.new(Websocket), :Port => 4000

!SLIDE

## public/javascripts/application.js
  
    @@@ Javascript
    $(document).ready(function(){
      Yact.initialize();
      $("form#send").submit(function(){
        // Remove user
        Yact.send({"content":$("input[type='text']").val()});
      });
    });

!SLIDE smbullets
## Auth findings

- Websocket API handles only disconnections or close of connections
- Does not understand 401 http response codes on handshake as treated as non-websocket speak
- Cramp limitations also - as websocket implementation overrides all requests and response to be websocket

!SLIDE smbullets

## Now you can expand...

- `User.connected` - associate with `current_user` and then compare when sending out messages
- Have the ability to know which users are authenticated and signed on