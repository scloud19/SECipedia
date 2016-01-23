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
      With Reflected/Stored the attack is injected into the application during server-side processing of requests where untrusted input is dynamically added to HTML. For DOM XSS, the attack is injected into the application during runtime in the client directly.

      Read: rendering_contexts.md

      Reflected/Stored XSS are server-side injection issues whereas DOM based XSS is a client (browser) injection issue.
      Exs:
        TODO: http://www.webappsec.org/projects/articles/071105.shtml
        /DOM_ATTACK_EXS/EX1.md
          When I tried this attack, the browser naturally escaped the <> in the script tag, so it couldn't work.  However, there might be ways around this.

          What's interesting about this example is how the parser echos out to the page when it finds unexpected inputs.

          In the ex, the client side code makes document.location vulnerable.  However, there can be others
            Ex: 
              document.URL
              document.URLUnencoded
              document.location (and many of its properties)
              document.referrer
              window.location (and many of its properties)
              etc.

          Mitigation
            Since the attack was sent as a query string, it made a round trip to the server, and thus the attack COULD be mitigated at the server level.

            However, if that query string was changed to a URL fragment, as seen below, this is changed.  The code then stays client side.

            Ex: url fragment
              http://www.some.site/page.html#default=<script>alert(document.cookie)</script>

              Then that stays client side only, and is much more difficult to detect.  This can have huge implications for SPAs.


      Occurance
        The attack payload is executed as a result of modifying the DOM "environment" in the victim's browser used by the original client side script, so that the client side code runs in an unexpected manor.
          In other words, the page itself does not change, but the client side code contained in the page executes differently due to the modifications that have occurred in the DOM environment.
            This is different than other XSS attacks (stored and reflected), in which the attack payload is placed in the response page (due to a server side flaw)




