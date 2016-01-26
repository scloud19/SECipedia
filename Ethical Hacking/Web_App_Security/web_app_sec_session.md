Ways that sessions are implemented
  Hidden form fields
  URL query strings
  Cookies

If a user doesn't make a request for a certain amount of time, the session should be expired.

Vulnerabilities in Sessions
  Get the session token = Can log in as the user

  Attacker can guess the session token

Other ways of session management (i.e. authentication)
  HTTP's built-in authentication (although sent in the clear)

  Application stores the state information on the client side rather than the server, usually in encrypted form.