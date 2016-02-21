Core ideas of Modsecurity
  Blend between WAF and IDS
  Allows realtime monitoring, logging, and access control.
  Apache module
  
Important usage scenarios

  Real-time security monitoring and access control
    Persistent storage mechanism
      Enables you to track system events time and perform event correlation

    Full request/response buffering
      Allows you to block requests

  Virtual patching
    Is the concept of vulnerability mitigation in a separate layer
      Allows you fix problems in apps without having to touch the application itself
        Aka, block traffic

  Full HTTP traffic logging
    Allows the logging of raw transaction data
      Very helpful in forensics.
    Allows you to select what parts of your logs should be sanitized.

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

