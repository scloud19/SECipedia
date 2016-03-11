Abbreviations
  MS - ModSecurity
  ! - A config in Apache's httpd.conf
    
Core ideas of Modsecurity
  Blend between WAF and IDS
  Allows realtime monitoring, logging, and access control.
  Apache module

  Everything in ModSecurity revolves around two items
    Configurations
      How does Modsecurity process the data it sees?
    Rules
      What to do with the processed data
      
  Main Functionality
    Parsing
      Modsecurity's supported data formats are backed by security-conscious parsers that extract bits of data and store them for usage in the rules.

    Buffering
      Both the request and response bodies are buffered
        Thus, the complete requests are seen before they're passed to the app
          The responses are seen before they're passed to the client

    Audit Logging
      Full transaction logging
        Ex: Request headers, request body, response headers, response body

      Allows you to selectively sanitize items of your logs

    Rule engine
      Also see modsecurity_rules_basics.md
      Builds on the work of all of the other components
        Takes these inputs and perform various actions

    Main items that Modsecurity Avoids
      Data sanitation (outside of logs)
        If you're being attacked, block the offending request/response entirely.
          Don't try to sanitize it, you're just opening up a new battlefield for loopholes.

  
Important usage scenarios

  Real-time security monitoring and access control
    NOTE: ModSecurity cant protect against traffic that's encrypted.
    
    Persistent storage mechanism
      Enables you to track system events time and perform event correlation

  Virtual patching
    Is the concept of vulnerability mitigation in a separate layer
      Allows you fix problems in apps without having to touch the application itself
        Aka, block traffic

  Continuous passive security assessment
    This is a variation of real-time monitoring, where, instead of focusing on the behavior of external parties, you focus on the behavior of the overall system.
      This is an early warning system that detects abnormalities and security weaknesses before they're exploited

  Web app hardening
    Attack surface reduction
      Where you narrow down the HTTP features that you accept
        Ex: request methods, headers, content types, etc.

  Out of the box ideas
    An XML web service router

Deployment Options
  Embedded
    As it's an Apache module, you can add it to any compatible version of apache that you're using as a web server

    Great option for
      People who dont want to introduce a new point of failure and want seamless scaling (with additional servers being added)
      Cases where implementing a separate proxy-based security layer is impractical
        Exs:
          Those who already have their architecture laid out and can't change it

          Protecting 100s of web servers
          

  Reverse proxy
    Def: Are HTTP routers that stand between web servers and their clients.

    Topology
      Install a dedicated Apache reverse proxy (with Mod security) between the browsers and your Apache web server

    This solution will get you a "proper" WAF
      You can leverage this to protect any number of web servers on the same network

      Separate security layer
        This is preferred by many security professionals
        Gives you isolation from the systems that you are protecting

      Performance
        Standalone ModSecurity will have resources dedicated to it
          You can have more complex rule sets

      CONS
        New point of failure
          Need to be addressed with high availability setup of multiple reverse proxies

Hybrid Nature of ModSecurity
  Mod Security relies on Apache for some of the work
    
    Apache role in ModSecurity (and all other modules)
      Decrypts SSL
      
      Breaks up the inbound connection stream into HTTP requests

      Partially parses HTTP requests

      Invokes ModSecurity
        Chooses the correct config context via the conf directives

      As a reverse proxy
        Forwards requests to backend servers (with/without SSL)
        
        Partially parses HTTP responses

        De-chunks the response bodies as necessary

Apache Security Considerations
  Apache does
    Request line and headers are NUL-terminated
      Normally, as what Apache doesn't see can't harm any module/app
        Although, NUL-byte evasion is a way to hide things, and this Apache behavior helps this process.

      Request Header Transformation
        Apache with combine multiple headers that use the same name
        Apache will collapse headers than span two+ lines

        May make it difficult to detect subtle signs of evasion
          In practice, this hasn't been a problem yet

      Quick request handling
        Sometimes modsecurity is unable to do anything but notice a request in the logging phase

        One ex: Invalid HTTP requests will be rejected by Apache without ModSecurity's input

      Restricted access to certain response headers
        Due to Apache's implementation the following response headers can't be inspected or logged
          Server
          Date


