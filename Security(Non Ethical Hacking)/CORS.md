https://developer.mozilla.org/en-US/docs/Web/HTTP/Access_control_CORS

General: A resource makes a cross-origin HTTP request when it requests a resource from a different domain than the one which served itself.
    Ex: An HTML page server from domain1.com makes an <img> src request for domain2.com/zachy.jpg  Many pages on the web today load resources like CSS stylesheets, images and scripts from separate domains.

    For security reasons, browsers restrict cross-origin HTTP requests initiated from within scripts (and other areas, as you'll see below)
      To get around this; CORS gives web servers cross-domain access controls, which enable secure cross-domain data transfers.

  Nutshell
    CORS is a specification that provides a way for an origin to ignore the Same Origin Policy.  
      In the most lenient configuration, a web application can allow a cross-origin XMLHttpRequest to access all of its resources (from any origin).
        Ex of "wide open" configuration:
          Will occur if these server-side HTTP headers are set:
            Access-Control-Allow-Origin: *
            Access-Control-Allow-Methods: POST, GET

            If a browser sends a cross-origin XMLHttpRequest to a server that doesn't have these headers, no access will be given to the response content.

      The HTTP headers inform the browser whether it is permitted access.



CORS is used to enable cross-site HTTP requests for:
  XMLHttpRequest (Javascript way of initiating requests)

  Web Fonts (for cross-domain font usage in @font-face within CSS)

  WebGL textures

  images/video frames drawn to a canvas using drawImage.

  Stylesheets (for CCSOM access)
    CCSOM - The CSS Object Model allows one to manipulate CSS from Javascript.  

  Scripts (for unmuted exceptions)

  General Overview
    CORS works by adding new HTTP headers that allow servers to describe the set of origins that are permitted to read that information using a web browser.  

    For HTTP methods that can cause side-effects on user data (ex. not GET, etc.), the specifications mandates that browsers "preflight" the request
      Preflighting
        This solicits supported methods from the server with an HTTP options request method.  If the server request is granted for a given HTTP verb, the browser will send the actual request with the given HTTP verb.

    Servers can also notify clients whether cookies and/or HTTP authentication data should be sent with requests.