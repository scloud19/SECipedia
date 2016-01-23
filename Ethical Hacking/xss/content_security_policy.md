CSP has been implemented to mitigate XSS vulnerabilities by defining a distinction between instructions and content.

The CSP HTTP headers:
  Content-Security-Policy
  X-Content-Security-Policy

  Are sent from the server and tell the browser where scripts can be loaded from.  Additionally, the CSP also stipulates the restrictions on those scripts; for example, if eval() can be used.

  