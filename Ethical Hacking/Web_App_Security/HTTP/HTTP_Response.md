HTTP Response
  Ex:
    HTTP/1.1 200 OK
    Date: Tue, 19 Apr 2011 09:23:32 GMT
    Server: Microsoft-IIS/6.0
    X-Powered-By: ASP.NET
    Set-Cookie: tracking=tI8rk7joMx44S2Uu85nSWc
    X-AspNet-Version: 2.0.50727
    Cache-Control: no-cache
    Pragma: no-cache
    Expires: Thu, 01 Jan 1970 00:00:00 GMT
    Content-Type: text/html; charset=utf-8
    Content-Length: 1067

    <html></html>

    General Notes
      The "reason phrase" aka OK can have any value so don't key off of it.
    
    Header notes
      Server
        The information contained here may/may not be accurate (if it is shown)

      Access-Control-Allow-Origin
        Indicates whether the resource can be retrieved via cross-domain Ajax requests.


    Cache-Control
      Passes caching directives to the browser
        Ex: no-cache

      ETag
        Specifies an entity tag

        Ex: See If-None-Match header is HTTP_Request.md

      Server
        Provides information about the web server software being utilized

      WWW-Authenticate
        When a server issues a 401, this is included as a header.

        This header provides the details on the types of authentication that the server supports.

      X-Frame-Options
        If and how the current response may be loaded within a browser frame.


      Set-Cookie
        Issues the browser a further cookie
          This is then submitted back in the Cookie header of subsequent requests to the server

      Pragma
        Instructs the browser not to store its response in its cache

        Expires
          In the ex above, this shows that the response content expired in the past and therefore shouldn't be cached

        A combination of these instructions are frequently issued when dynamic content is being returned to ensure that browsers obtain a fresh version.

      Content-Type
        What's the content of the message's body.  In the payload above, it's text/html;
                    

