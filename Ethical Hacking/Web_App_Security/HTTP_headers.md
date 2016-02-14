Main idea
  The address and other instructions that are written on an envelope.
    Where should the package go?
    Who should the package be handled?

  Headers and included on the "messages" between the client and server (both ways)

  The content of the headers determines how the content that follows is processed (either by the browser or the web server.)
  
  Some headers are required, some are optional


Important Headers
  X-Content-Type-Options: nosniff
    In websites that allow uploads from untrusted users (which can be downloaded by other users)

    In certain situations, a user could upload a text file, that would be "content sniffed" to a HTML content type and then rendered in the browser (if a user tried to open it up).  Thus, if this header was used with:
      Content-Type: text/plain;

      Newer Chrome/IE browsers would force the content to be rendered as text/plain


  Strict-Transport-Security
    This header instructs the browser that communication to the website must occur over a valid HTTPS tunnel.  

    It will not be possible for the user to accept any HTTPS errors and then proceed over an insecure connection.
      If this occurs, the browser will explain the error without allowing the user to continue browsing.

    Note: If this is to occcur, i'd suggest just turning off HTTP at the server level.

    X-Frame-Options
      This header is used to prevent the page from being rendered in an iframe.
        This header is utilized to help mitigate UI redressing attacks (Ex: Click Jacking)

  Referer Header
    Used to indicate the URL of the page from which the current request originated.
      This can occur when a user
        clicks a hyperlink
        submits a form
        the page referenced other resources such as images
          If you're an image provider, you might have a lot of juicy information in the form of URL params (session tokens, etc.)


When you attempt to modify server responses coming to the client, you might get this HTTP message in your proxy

  HTTP/1.1 304 Not Modified
  Date: Wed, 6 Jul 2011 22:40:20 GMT
  Etag: “6c7-5fcc0900”
  Expires: Thu, 7 Jul 2011 00:40:20 GMT
  Cache-Control: max-age=7200

  Thus, there isn't any "payload" portion of the message to adulterate.

  The aforementioned response occurs because the browser already has a cached copy of the resource that it requested.

  If the browser requests a resource (that was previously cached) it typically adds two headers to the request:
    If-Modified-Since
    -If-None-Match

  Ex:
    GET /scripts/validate.js HTTP/1.1
    Host: wahh-app.com
    If-Modified-Since: Sat, 7 Jul 2011 19:48:20 GMT
      * Tells the server when the browser last updated its cached copy

    If-None-Match: “6c7-5fcc0900”
      * "6c7.." This is called the Etag string, which the server provided with that copy of the resource.
        You can think of it as a tracking number for each cacheable resource.  Each time the resource is modified, this number is updated.

      If the server possesses a newer version of the resource than the date specified in the If-Modified-Since header OR if the etag of the current version is different than the one specified in the If-None-Match header, the server will respond with the requested resource.  Otherwise, it will return a 304 which tells the browser (use your cached copy)


    If you need to get around a 304 so you can modify the payload returned from the server
      Intercept the request and remove to 2 aforementioned headers
        Burp contains an option to strip these headers from every request

