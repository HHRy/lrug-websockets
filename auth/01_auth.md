## Authentication
How
- Websocket spec makes sure that the cookies for a site is sent on the websocket requests to be used for authentication
- So if an authenticated cookie is provided, it will be sent when attempting to connect to the websocket server.
- We will then need to modify the front end to authenticate someone and then the backend to check that cookie token.

The websocket API doesn't understand much, so sending back a 401 unauthorized will just result in either the connection waiting for a correct websocket response (which is one with the websocket 'upgrade' headers in)

Middleware
- Create a middleware rack class
	// this is one way we can do this.
-? In our example have rack Middleware to process the cookie token and handle correctly.
- This can be used on both the cramp controllers and sintra but dealing with the un-authenticated requests be different.

- Create a dummy account, that's accessible from both the server and frontend.
	// Something for now - can use something more fancy if you like.
	Account.authenticate(username, password)
	Account.authenticate_by_token(token)

On front end
- Login -> authenticate and set_cookie("token","asd")
- If un-authenticated not show chat page.

Then on backend
- Check in the on_start part of the connection...
- If no token supplied, send error message and finish the response, this makes sure the connection closes.
	// confirm on whether this will close the TCP connection, this makes sure

- Websocket to notify the user of an un-authentication and then to close the connection.
- Don't want the browser to waste effort keeping it open.