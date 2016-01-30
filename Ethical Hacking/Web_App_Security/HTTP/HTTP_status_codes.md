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


    5xx
      The server encountered an error fulfilling the request


    