HTTP Requests
  All HTTP messages (requests and responses) consist of one or more headers, each on a separate line, followed by a mandatory blank line, which is followed by an optional message body.

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
    
    Additional Notes
      GET requests don't have a message body

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