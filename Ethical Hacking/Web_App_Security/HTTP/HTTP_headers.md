General Headers
  To see more granular headers for requests/responses, look at HTTP_Requests.md,etc.

  The following headers will be useful in web application attacks

  Connection
    Tells the other end of the communication whether it should close the TCP connection after the HTTP transmission has completed or keep it open for further messages.
  
  Content-Encoding
    Specifies what kind of encoding is being utilized for the content contained in the message body
      Ex: gzip (compression)

    Content-Length
      Specifies the length of the message body, in bytes
        Exception: In the case of responses to HEAD requests, this Content-Length header shows the number of bytes in the message field that WOULD have been returned, if the request was a GET.

    Content-Type
      The type of content contained in the message body
        Ex: text/html for HTML documents

    Transfer-Encoding
      Any encoding that was performed on the message body to facilitate its transfer over HTTP.
        Ex: chunked encoding



