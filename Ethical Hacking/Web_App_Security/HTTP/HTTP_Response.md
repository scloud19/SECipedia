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

    Notes
      The "reason phrase" aka OK can have any value so don't key off of it.

      Server header
        The information contained here may/may not be accurate (if it is shown)

      Set-Cookie header
        Issues the browser a further cookie
          This is then submitted back in the Cookie header of subsequent requests to the server

      Pragma header
        Instructs the browser not to store its response in its cache

        Expires header
          In the ex above, this shows that the response content expired in the past and therefore shouldn't be cached

        A combination of these instructions are frequently issued when dynamic content is being returned to ensure that browsers obtain a fresh version.

      Content-Type header
        What's the content of the message's body.  In the payload above, it's text/html;
                    
