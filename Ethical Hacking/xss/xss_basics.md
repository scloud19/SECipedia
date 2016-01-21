Basics
  XSS attacks are a type of injection attack. This is usually done with malicious browser side scripts that are "injected" into other users' browsers.

  Official OWASP Definition

    1) Data enters a Web application through an untrusted source, most frequently a web request.
    
    2) The data is included in dynamic content that is sent to a web user without being validated for malicious content.
      This validation process needs to include proper encoding

  Attack Surface
    Attacker can access:
      Cookies
      Session Tokens

    Rewrite the content of the HTML page

    Redirecting a user to an attacker's server/website

    Content types of exploits
      Javascript
        Usually the case
      HTML
      FLASH

      Any type of code that the browser must execute.


  Types of XSS Attacks
    Ex: Stored XSS, Reflected XSS, DOM Based XSS

    Stored XSS (aka Persistent or Type 1 XSS)

      Where the injected script is permanently stored on the target servers.
        Ex: Storage in db, message forum, visitor log, comment field, etc.

        When the victim loads up the site, this stored script is executed by the browser

    Reflected XSS (aka Non-Persistent or Type-II XSS)

      "Reflected off the web server"
      Are attacks where the injected script is reflected off the web server via a error message, search result, or any response that includes some or all of the input sent to the server as a part of the request.
        Ex: A user clicks a link with an invalid url (thats actually a script tag with malicious code).  When the website is loaded, the error message can include this script tag, which the browser loads because it came from a "trusted" server.

      Delivery Ex:
        Email message, or some other web site.  Can input these attacks into tinyURL's, etc.
        
    DOM Based XSS ("type-0 XSS")
      Occurance
        The attack payload is executed as a result of modifying the DOM "environment" in the victim's browser used by the original client side script, so that the client side code runs in an unexpected manor.

