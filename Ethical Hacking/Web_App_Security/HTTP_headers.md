Main idea
  The address and other instructions that are written on an envelope.
    Where should the package go?
    Who should the package be handled?

  Headers and included on the "messages" between the client and server (both ways)

  The content of the headers determines how the content that follows is processed (either by the browser or the web server.)
  
  Some headers are required, some are optional


Important Headers
  X-Content-Type-Options: nosniff
    In websites that allow uploads from untrusted users (which can be downloaded by other users)

    In certain situations, a user could upload a text file, that would be "content sniffed" to a HTML content type and then rendered in the browser (if a user tried to open it up).  Thus, if this header was used with:
      Content-Type: text/plain;

      Newer Chrome/IE browsers would force the content to be rendered as text/plain


  Strict-Transport-Security
    This header instructs the browser that communication to the website must occur over a valid HTTPS tunnel.  

    It will not be possible for the user to accept any HTTPS errors and then proceed over an insecure connection.
      If this occurs, the browser will explain the error without allowing the user to continue browsing.

    Note: If this is to occcur, i'd suggest just turning off HTTP at the server level.

    X-Frame-Options
      This header is used to prevent the page from being rendered in an iframe.
        This header is utilized to help mitigate UI redressing attacks (Ex: Click Jacking)

  Referer Header
    Used to indicate the URL of the page from which the current request originated.
      This can occur when a user
        clicks a hyperlink
        submits a form
        the page referenced other resources such as images
          If you're an image provider, you might have a lot of juicy information in the form of URL params (session tokens, etc.)