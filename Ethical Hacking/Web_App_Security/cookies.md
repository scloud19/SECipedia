Cookies
  Key part of the HTTP protocol that most web apps rely on
  Frequently are used for exploitation

  Can be any string that doesn't contain a space

  General Idea
    Cookies allow the server to send items of data to the client
      The client stores these values and resubmits them on every request

  Set-Cookie
    the response header that sets a cookie on the client

    To set multiple cookies, utilize multiple "Set-Cookie" headers


    Client responds with the "Cookie" header
      Multiple cookies are all sent in the cookie header, but they're delimited by a ;

    The Set-Cookie header can contain any of the following optional attributes
      
      expires
        Sets a date which the cookie is valid.
        This causes the browser to save the cookie to persistent storage

        If this is not set, the cookie is used only in the current browser session.

      domain
        Sets the domain for which the cookie is valid.

        This must be the same (or a parent of) the domain from which the cookie is received.  

      path
        Specifies the URL path for which the cookie is valid

      secure
        The cookie will be submitted only in HTTPS requests

      HttpOnly
        Instructs the browser to disallow access to the cookie content from any scripts.  Thus it is more difficult to do cookie theft from XSS.

