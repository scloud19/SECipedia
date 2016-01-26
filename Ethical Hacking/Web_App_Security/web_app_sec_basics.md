Core Security Problem: Users can submit any arbitrary input to the web application.
  This is because the client is outside of the application's control; thus it must always be UNTRUSTED.

  Manifestations
    Users can interfere with any piece of data that's transmitted between the client and the server
      Ex: request params, cookies, HTTP headers.  Client side validations can easily be circumvented.

    Users can send requests in any sequence and can submit parameters at a different stage than the application expects, more than once, or not at all.  Any assumption that the developer makes about user interaction can be violated.

    Users are not restricted to accessing the web application through the browser, numerous outside tools can be used to help attack web applications.
      These tools can make requests that no browser would ordinarily make and can create large numbers of requests quickly.


Why do we have so many security problems?
  No security awareness
  
  Custom Development

  Deceptive Simplicity
    It is now possible for a novice programmer to create a powerful application from scratch in a short period of time.
    Big difference between producing code that is functional and code that is secure.

  Rapid reuse of third party modules
  Rapidly Evolving Threat Profile
  Resource and Time Constraints
