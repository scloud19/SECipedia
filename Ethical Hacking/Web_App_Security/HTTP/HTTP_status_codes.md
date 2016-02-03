Status Codes
  Each HTTP response must contain a status code in its first line.

  (The specific status codes listed below are more specific to web application attacks)

  Status code groups
    1xx
      Informational

      Ex:
        100 Continue
          This allows a client that is sending a request message (with a request body) to determine if the server is willing to accept the request (based on the request headers) before the client sends the request body.
    2xx
      The request was successful

      Ex:
        200 OK
          The request was successful and that the response body contains the result of the request

        201 Created
          If a put request was issued, this indicates that the request was successful
    3xx
      The client is redirected to a different resource

        Ex:
          301 Moved Permanently
            Redirects the browser permanently to a different URL (which is specified in the Location header.)
              The client should use the new URL in the future rather than the original.

          302 Found
            Redirects the browser temporarily to a different URL (which is specified in the Location header).  In subsequent requests, the client should revert to the original URL.

          304 Not Modified
            Instructs the browser to use its cached copy of the requested resource.
              The server leverages If-Modified-Since and If-None-Match request headers to determine whether the client has the latest version of the resource.

    4xx
      The request contains an error of some kind

      Ex:
        400 Bad Request
          The client submitted an invalid HTTP request
            Ex: Placing a space character into the URL, etc.

        401 Unauthorized
          The server requires HTTP authentication before the request will be granted.
            WWW-Authenticate header
              Will specify what types of authentication will be provided.

        403 Forbidden
          Indicates that no one is allowed to access the requested resource (regardless of authentication)

        404 Not Found
          Indicates that the requested resource does not exist

        405 Method Not Allowed
          Indicates that the method used in the request is not supported for the specified URL.
            Ex: Utilizing a DEL when it's not supported

        413 Request Entity Too Large
          Indicates that the body of your request is too large for the server to handle
            Ex: This could happen an attacker is probing for buffer overflow vulnerabilities in native code.  And is therefore submitting long strings of data.

        414 Request URI Too Long
          Similar to 413

          Indicates that the URL used in the request is too large for the server to handle

    5xx
      The server encountered an error fulfilling the request

      Ex:
        500 Internal Server Error
          The server encountered an error fulfilling the request.

          Ex: You have submitted unexpected input that caused an unhandled error somewhere within the application's processing.

          In these cases, you should closely review the full contents of the server's response for any details on the error.


        503 Service Unavailable
          Usually, this means that; Although the web server is functioning (and can respond to requests), the application accessed via the server is not responding.
            If you see this, you should verify whether this is the result of any action that you have performed.

        
    Exceptions
      Don't assume that when the app responds with a 200 for an existing piece of content (similar logic can be applied to other status codes as well).

      During enumeration for hidden content (and other exercises) keep these potential meanings in mind

        302 Found
          If redirected to a login page, the resource may be accessible to only authenticated users.

          If redirected to an error page, this may indicate a non-existent file, etc.

          If redirected to another location, the redirect may be a part of the apps intended logic (and should be investigated further)


        400 Bad Request
          The application may use a custom naming scheme for directories/files within URLS (and your request is "out of line").
            More likely, is the fact that the wordlist you are using for you enumeration contains some whitespace characters or other invalid syntax.

        401 Unauthorized or 403 Forbidden
          Usually indicates that the requested resource exists but may not be accessed by any user (regardless of the auth status or priv level)

          This often occurs when directories are requested

        500 Internal Server Error
          During content discovery, this usually indicates that the application expects certain params to be submitted when requesting the resource






    