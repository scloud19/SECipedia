When attacking web applications, the most common methods leveraged are GET and POST
Methods
  GET
    Designed to retrieve resources

    Can be used to send parameters to the requested resouce in the URL query string

    URLs are displayed on-screen and are logged in the browsers history and the server's access logs

    These requests are also transmitted in the Referer header to other sites when external links are followed.
      If sensitive information is kept in the URL, it can be easily seen in the referer header

    For aforementioned reasons, the query string shouldn't be used to transmit any sensitive data.

  POST
    Designed to perform actions
      As such, if a request is initiated with this method and the user clicks the 'back' button, the browser will not automatically reissue the request (it provides a warning instead)

    With this method, request params can be sent both in the URL's query string and in the body of the message.

    The URL can be bookmarked, but any params sent in the message body will be excluded. Usually, these message params are also not in the web server logs.

  HEAD
    Same as a GET request, except that the server shouldn't return a message body in its response

    Can be used to see if a resource is available before making a GET request

  TRACE
    Used for diagnostic purposes

    The server should return in the response body the exact contents of the request message it received.
      This can be used to detect the effect of any proxy servers between the client that may manipulate the request.

  OPTIONS
    Report the HTTP methods that are available for a particular resource.
      Ex Response:
        Usually contains an Allow header that lists the available methods.

  PUT
    Attempts to upload the specified resource to the server, using the content that's contained in the request's body.

    If enabled, you may be able to leverage it to attack the application
      Ex: upload a script to execute on the server
 