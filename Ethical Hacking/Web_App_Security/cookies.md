Secure Cookie Flag
  Purpose of this flag is to instruct the browser to never send the cookie over any unsecured channel.  Thus, a sensitive session token can remain encased in an encrypted barrier whevenever it is in transit.

HttpOnly flag
  Instructs the browser to disallow access to the cookie content from any scripts.  Thus it is more difficult to do cookie theft from XSS.