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
        Ex: text/html
              for HTML documents

            application/x-www-form-urlencoded
              The parameters (from the form) are represented in the message body as name/value pairs in the same structure as a url query string
                ex: username=yo&password=supdude&submit=log+in 

            multipart/form-data
              An application can request that browsers use multipart encoding by specifying this in an enctype attribute in the form tag.
                ex: <form enctype='multipart/form-data'>

                When this occurs, the Content-Type header in the reuest also includes a random string that's utilized as a separator for the parameters in the request body.

                  Ex request:
                    // Assuming we have a data format like the string above

                    POST /secure/login.php?app=quotations HTTP/1.1Host: wahh-app.comContent-Type: multipart/form-data; boundary=------------7d71385d0a1a
                    Content-Length: 369
                    Cookie: SESS=GTnrpx2ss2tSWSnhXJGyG0LJ47MXRsjcFM6Bd

                    ------------7d71385d0a1a
                    Content-Disposition: form-data; name=”username”

                    yo
                    ------------7d71385d0a1a
                    Content-Disposition: form-data; name=”password”

                    supdude


    Transfer-Encoding
      Any encoding that was performed on the message body to facilitate its transfer over HTTP.
        Ex: chunked encoding



