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

    With this method, request params can be sent both in the URL's query string and in the body of the message.

    The URL can be bookmarked, but any params sent in the message body will be excluded. Usually, these message params are also not in the web server logs.

