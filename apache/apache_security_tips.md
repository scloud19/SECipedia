Security Tips In Apache
  Always stay updated!
    Subscribe to the main mailing list for various posts on server upgrades and security issues (important issues only)
    Not a discussion thread, only messages are posted through the foundation
        announce-subscribe@httpd.apache.org

        announce-help@httpd.apache.org
          To get help with the list

    Third party code
      Most of the time, if there's a vulnerability, it isn't in the Apache Server code.  It's from add-on code, CGI scripts, or the OS.
        You must keep abreast of updates/vulnerabilities to these items.


  Denial of Service (DoS) Attacks
    Try to prevent responses to clients by typing up resources on the server.

    You can't prevent these attacks entirely, but you can do create mitigating factors.

    Create a multi-tier solution 
      In addition to the security tips outlined here, it's very important to have correct firewall configurations
        We will look at this in depth in another lecture.

        Many firewalls can be configured to restrict the number of simultaneous connections from any unique IP/network.
          This isn't effective against DDoS attacks (Distributed Denial of Service Attacks)

    Server Configs that can help
      
      RequestReadTimeout directive
        Sets a limit for the amount of time a client can take when finishing the request

      TimeOut Directive
        Has a larger footprint then the RequestReadTimeout directive.

        If receiving a DoS attack, one should look at this config first.

