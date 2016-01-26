Core Security Problem: Users can submit any arbitrary input to the web application.
  This is because the client is outside of the application's control; thus it must always be UNTRUSTED.

  Manifestations
    Users can interfere with any piece of data that's transmitted between the client and the server
      Ex: request params, cookies, HTTP headers.  Client side validations can easily be circumvented.

    Users can send requests in any sequence and can submit parameters at a different stage than the application expects, more than once, or not at all.  Any assumption that the developer makes about user interaction can be violated.

    Users are not restricted to accessing the web application through the browser, numerous outside tools can be used to help attack web applications.
      These tools can make requests that no browser would ordinarily make and can create large numbers of requests quickly.


Why do we have so many security problems?
  Remember, every feature added is adding to the attack surface
  
  No security awareness
  
  Custom Development

  Deceptive Simplicity
    It is now possible for a novice programmer to create a powerful application from scratch in a short period of time.
    Big difference between producing code that is functional and code that is secure.

  Rapid reuse of third party modules
  Rapidly Evolving Threat Profile
  Resource and Time Constraints


Changing the of the security parameter
  For example, suppose that an attacker wants to “hack in” to a bank’s systems and steal money from users’ accounts. In the past, before the bank deployed a web application, the attacker might have needed to find a vulnerability
  in a publicly reachable service, exploit this to gain a toehold on the bank’s DMZ, penetrate the firewall restricting access to its internal systems, map the network to find the mainframe computer, decipher the arcane protocol used to access it, and guess some credentials to log in. However, if the bank now deploys a vulnerable web application, the attacker may be able to achieve the same outcome simply by modifying an account number in a hidden field of an HTML form.

  Now, the web browser can be used to attack a user on an internal network

Basic trends in security
  The shift in attention of attacks
    From server-side to those that target application users.
    As awareness of security threats increases, flaws in the server side are the first to be well understood and addressed, leaving the client as a key battleground.