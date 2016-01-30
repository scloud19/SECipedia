HTTP Proxy
  Is a server that mediates access between the browser and the web server.

  Browser initiating a connection with a proxy
    The browser makes an HTTP request to the proxy server using the CONNECT method and specifying the destination hostname and port number as the URL.
      If the proxy grants the request, it returns an 200 response, keeys the TCP connection open, and from that point acts like a pure TCP-level relay to the destination web server.

  By some standards, leveraging an HTTP proxy is the most useful item for an attacker as he could modify all requests and responses, even those using HTTPS.

  Ex of additional services that a proxy can offer
    Caching
    Authentication
    Access Control

  If a browser has been configured to use a proxy server, it makes all its requests to that server.
    Then, the proxy relays the requests to the relevant web servers and forwards their responses back to the browser.

  2 Core differences in how HTTP works when being utilized in a proxy
    
    If the browser submits a:
      HTTP Request
        It places the full URL into the request.  The proxy server extracts the hostname and port and uses those to direct the request to the correct destination web server.

      HTTPS Request
        The browser can't perform the SSL handshake with the proxy server because this would break the secure tunnel and leave the communication open to interception.
          Thus, the browser must use the proxy as a pure TCP-level relay, which passes all network data in both directions between the browser and web server (which which the browser performs an SSL handshake as normal)

