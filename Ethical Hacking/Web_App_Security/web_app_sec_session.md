Core use
  Implemented after a login
    Enables the app to auth/ID a given user across a number of different requests into the future
    Handles state of that user's interaction with the app

Juicy Target
  Can bypass very secure auth mechanisms (i.e. smartcards, etc.)
    

Simplest bypass
  Increment the value of a token issued to a valid user to take on the ID of another

Ways that sessions are implemented
  Hidden form fields
  URL query strings
  Cookies

If a user doesn't make a request for a certain amount of time, the session should be expired.

The overall session data can be stored on the client or server.
  If it's on the client, it obviously needs to be encrypted.

  A session token keys a session's data to specific user across multiple requests.

Vulnerabilities in Sessions
  Get the session token = Can log in as the user

  Attacker can guess the session token

Other ways of session management (i.e. authentication)
  HTTP's built-in authentication (although sent in the clear)

  Application stores the state information on the client side rather than the server, usually in encrypted form.