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

        



    