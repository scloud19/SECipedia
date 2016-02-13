Javascript
  Exploits/Quirks could exist on the client and the server (Node, etc.)
  
  Many devs use parseInt/parseFloat to parse input.
  When an individual places in a comma, only the numbers before the comma are returned.

HTTP Headers
  Referer header
    Some apps perform additional processing on the Referer header.  
      
      Ex: an application may detect that a user has arrived via a search engine and seek to provide a customized response tailored to the user's query.
        For SEO, some applications will try to add content such as HTML keywords to the page (which are created through a user's search term in the referer header).  
          Thus, they would be open to injection risks.

  User-Agent header
    By inspecting this header, some applications serve different content (mobile device, etc.)

    Besides potential injection risks, this header allows you to potentially access a simpler interface (mobile) that could allow for different attacks (and interfaces such as mobile.. whos code has gone through less security testing)

      Tools
        Burp Intruder
          Contains a built-in payload list which contains a large number of user agent strings.
            Leverage this and inspect the results to see if a different user interface is being presented.


  X-Forwarded-For request header
    DEF: A common method for IDing the originating IP address of a client connecting to a web server through an HTTP proxy/load balancer

    Usually, the IP address of the client's network connection is available to the server via an API.
      However, sometimes this isn't feasible if the application resides behind a load balancer or proxy.
        In this case, an application may use the IP address specified in the X-Forwarded-For request header (if it's present)
          Some developers may then mistakenly assume that the IP address value is untainted and process it in dangerous ways.
            This opens up SQLi and XSS, etc.





Out-of-Band Channel
  A class of entry points for user input.
  These types of entry points are harder to discover, and simply watching the HTTP traffic might not show them.
    Usually, finding these requires an understanding of the wider context of the functionality that the application implements.

  Exs:
    A web main app that processes/renders e-main messages via SMTP

    A publishing app that contains a function to retrieve content via HTTP from another server

    Any kind of app that provides an API interface for use by non-browser user agents (ex: cell phone apps, etc), if the data processed via this interface is shared with the primary web app.

Enumeration
  When enumerating, make sure that you have software that specifically enumerates the web server (ex: phpmyadmin routes, etc.) AS well as application level enumeration (/admin routes).  You want to test both layers, so make sure that your tool does both.
    Remember, in certain enumeration apps, they can be configured using an IP or domain (for the target).
      Sometimes, if you use an IP (it'll use that as its "domain") and wont crawl the real domain of the site (it'll see it as external).  Crawl the domain with both IP and domain in mind.



fileName param (in URL, etc.)
  Also see google_hacking.md
  
  utilize with a directory traversal attack or leverage to find log files, etc.

  Put in an invalid filename to see if error is occurs.
    If so, look for path of "file not found" and leverage in directory traversal attack.
      If the fileName param is being directory appended to a preexisting directory path, simply ass "../../", etc. to bust out of the current directory.

      Ex: 
        Certain web frameworks house "juicy" files at predefined locations.  
          ASP.NET
            look for: web.config file
          Windows
            system.ini

Also see attack_categories.md for additional details

Changing the price of a product that is transmitted in a hidden HTML form field to fraudulently purchase the product for a cheap amount.

Robots.txt
  Usually holds juicy information for /admin routes,etc.  

If you compromise a user's email account, you have access to everything.

Access Control Flaws
  Often the developer makes wrong assumptions about how a user will interact with the application and will omit access control checks from some application functions.

  To find these, it is tedious.  You need to do probing on every independent part of the application.

Tokenizing Flaws
  Usually created when trying to blacklist

  exs:
    SELECT/*foo*/username,password/*foo*/FROM/*foo*/users
      Utilizing comment syntax


    <img%09onerror=alert(1) src=a>
      Using encoded characters


  Black list filters in WAFs
    Vulnerable to NULL byte attacks
      Inserting a NULL byte anywhere before a blocked expression can cause some filters to stop processing the input and therefore not identify the expression.
        Ex: %00<script>alert('muhah')</script>

      These types of attacks are relatively common in the web app security world.
        As the NULL byte can act as a string delimiter (in certain contexts), it can be used to terminate a filename or a query to some back-end component.

    In some browsers, NULL bytes are simply ignored; so arbitrary null bytes can be inserted with blocked expressions to defeat certain blacklist-based filters.

When crafting input for an attack, think of the ways that the data can be potentially transformed before any white list/sanitizing is met.
  Ex: Is recursive sanitizing in place?
    If not: <scr<script>ipt> becomes <script>

  Ex: If the application first removes ../ recursively and then removes ..\ recursively then the following input can be used to defeat the validation
      ....\/

      If recurive sanitization occurs, when if one one problematic character leads to another.  There could be an recurive loop that crashes the server.

In many apps, admin functions are implemented within the app itself (and is accessible through the same web interface as its core functionality)
      Thus greatly opens up the attack surface.
        Often times, apps dont implement effective access control of some of their admin functions.  Thus, can attacker may find a means of creating a new user account with powerful privs.

        Admin functions usually involves displaying data that originated from ordinary users.  Any XSS flaws within that admin interface can be a huge point of concern.

        Admin functionality is often subjected to less stringent security testing because its users are "trusted" or pen-testers are given access to only low-privileged accounts.

HTTP Responses
  301 Moved Permanently
    Redirects the browser permanently to a different URL (which is specified in the Location header.)
      The client should use the new URL in the future rather than the original.

    Any way to inject MtM a response and inject a 301 to another domain?


HTTP Headers
  In general, developers assume that the HTTP headers are more "tamper proof" and thus they might not put sanitization mechanisms on these inputs.
  Expires, Pragma headers
    Are leveraged to tell the browser to cache the response.  If you're doing an attack, you can always instruct the clients to cache the contents so when the website changes it's code, it doesn't really matter (unless the cache is flushed).

  Set-Cookie
    The domain flag of this header specifies the domain for which the cookie is valid.  This must be the same or a parent of the domain from which the cookie is received.
        So, if a webhost hosts websites on subdomains (*.webhost.com). Could an attacker access the cookies that were set on the parent (assuming the path flag isn't set)?


  Refer
      If sensitive information is kept in the URL, it can be easily seen in the referer header if a link is followed.
          What about a person on a website with a sensitive URL and then they click on an ad link, this information can be leaked to the ad's server.  
              Implications for HTTPS? I believe that it is obfuscated from view in this context.

  For other idea of potential attacks
    Look in HTTP_Methods.md


Create a public proxy server on AWS that injects affiliate cookies into browsers.  This gives attackers a financial edge.