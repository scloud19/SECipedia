Mixed Content vulnerabilities
  Websites with this issue have an origin using HTTPS but request content over HTTP.

Ways that the server could reduce the security of the web browser (aka YOUR environment)
  Define a promiscuous Cross-origin Resource sharing (CORS) policy which relaxes the SOP and allows any other site access to the DOM.

  Not setting the X-Frame-Options header, HttpOnly or Secure cookie flags, leaving pages vulnerable to Clickjacking attacks and cookies accessible from JavaScript or via plaintext channels respectively. 

  Not using a Content Security Policy (CSP) headers allowing remote origins to be used to embed scripts, or allowing directives such as unsafe-eval. 
    Ex: XSS would be allowed, etc.