!SLIDE

# Authentication

!SLIDE smbullets

## Authentication
- Websocket API outlines that cookies to be sent on websocket connection handshake
- Backend can use this cookie to authenticate websocket connection
- Frontend can set cookie
- Cramp and Sinatra are rack - middleware

!SLIDE

## user.rb

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

## auth.rb - rack middleware

    @@@ Ruby
    require 'user'

    class Auth
      module Helper
        def current_user
          request.env['user']
        end
      end

      def initialize(app)
        @app = app
      end

      def call(env)
        env['user'] = authenticate(env)
        @app.call(env)
      end

      private

      def authenticate(env)
        request = Rack::Request.new(env)
        token = request.cookies["token"] || request.params["token"]
        current_account = User.authenticate_by_token(token)
        current_account ? current_account : nil
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

    get '/login' do haml :login end

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
    ...
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
      ...
    end

    Rack::Handler::Thin.run Auth.new(Websocket), :Port => 4000

!SLIDE smbullets
## Auth findings

- Websocket API handles only disconnections or close of connections
- Does not understand 401 http response codes on handshake as treated as non-websocket speak
- Cramp limitations also - as websocket implementation overrides all requests and response to be websocket

!SLIDE smbullets

## Now you can expand...

- `User.connected` - associate with `current_user` and then compare when sending out messages
- Have the ability to know which users are authenticated and signed on