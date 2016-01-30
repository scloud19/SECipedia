HTTP Requests
  All HTTP messages (requests and responses) consist of one or more headers, each on a separate line, followed by a mandatory blank line, which is followed by an optional message body.

  Send parameters to the server mainly in these ways (but any input can be used):
    URL query string
    File path of REST-style URLs
    Cookies
    Body of requests using POST

      Except exs:
      Using the User-Agent header to serve content based on browser type

  Ex: 
    GET /auth/488/YourDetails.ashx?uid=129 HTTP/1.1
    Accept: application/x-ms-application, image/jpeg, application/xaml+xml, image/gif, image/pjpeg, application/x-ms-xbap, application/x-shockwave-flash, */*
    Referer: https://mdsec.net/auth/488/Home.ashx
    Accept-Language: en-GB
    User-Agent: Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 6.1; WOW64; Trident/4.0; SLCC2; .NET CLR 2.0.50727; .NET CLR 3.5.30729; .NET CLR 3.0.30729; .NET4.0C; InfoPath.3; .NET4.0E; FDM; .NET CLR 1.1.4322)
    Accept-Encoding: gzip, deflate
    Host: mdsec.net
    Connection: Keep-Alive
    Cookie: SessionId=5B70C71F3FD4968935CDB6682E545476
    // Blank line

    // Optional Message Body
    
    Additional general notes
      GET requests don't have a message body

    Additional header explanations

      Accept
        Tells the server what kinds of content the client is willing to accept
          Ex: image types, flash, etc.

      Accept-Encoding
        Tells the server what kinds of content encoding that the client can accept

      Authorization
        Submits credentials to the server for one of the built-in HTTP auth types.

      Cookie
        Submits cookies to the server
          Remember these were cookies that the server previously issued

      Host
        Specifies the hostname that appeared in the full URL being requested

      If-Modified-Since
        Specifies when the browser last received the requested resource
          If the resource has not changed since that time, the server may instruct the client to use its cached copy
            This is done by issuing a 304 status code

      If-None-Match
        Specifies an entity tag.
          This is an identifier denoting the contents of the message body.
        Ex:
          When the server gives a response, it keys the HTTP Response body with a unique identifier and sends that entity tag in the response.
            Then when the browser requests a given resource, it sends the last received entity tag for a given resource.

          This sequence of events helps the server to determine whether the browser may use its cached copy of the resource.

      Origin
        Utilized in cross-domain Ajax requests to indicate the domain from which the request originated.


      Referer: 
        I.e., Where did the user come from?  If a user clicked a 
        link, this header would be populated with the original 
        site's url.  This header was misspelled in the original

        (This was misspelled in the original Spec, and has maintained this spelling ever sense).

      User-Agent:
        For legacy reasons, most browsers include the Mozilla prefix.
          This was the User-Agent string used by the once dominant Netscape browser, and other browsers wanted to assert that they were compatible with this standard.
            Ex: The example payload is from IE
        


      The only HTTP versions in common use on the Inernet are 1.0 and 1.1
        Most browsers use version 1.1 by default.

        Differences in 1.0 and 1.1
          While there are differences between the two versions, the main difference when attacking web apps is that in version 1.1 the Host request header is mandatory.