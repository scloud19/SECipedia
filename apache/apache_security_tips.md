T: Action for tutorial

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

  Include a WAF module
    Ex: http://modsecurity.org/

    I'll be doing a lecture on firewalls in the future, but this can help for now.

  Server Side Includes (SSIs)
    Are pieces of code that are placed in HTML pages, and are evaluated on the server before the requested pages are sent to the user.

    Not enabled by default.

    For example, you can display the current time to an html page by adding:

    <!--#exec cmd="/usr/bin/date" -->

    exec cmd
      Will run a command on the linux server as the apache user/group (if that user was specified in the httpd.conf).  Obviously this can be a huge security risk for a shared hosting environment.
        This is also true for modules that include other content, including: mod_php, mod_perl, mod_python, etc.

    Potential Help
      suEXEC
        Provides users of httpd the ability to run CGI and SSI programs under user IDs that are different from the user ID of the web server (i.e., the "apache" user).
          Ex: This can dramatically reduce security holes because all user initiated CGI and SSI programs run as the web server.  By locking down an SSI/CGI program to a unique user, we can give much more granular permissions.

        https://httpd.apache.org/docs/2.4/suexec.html

        CAUTION:  The configs aren't the most intuitive.  If you make a small mistake, the security consequences can be catastrophic. 

      When turning on SSI, don't turn on this feature for *.htm or *.html
        Not only for security but also for performance.
        Any file extension that is SSI-enabled has to be parsed (line-by-line) from httpd.

        Turn on for only a small subset of files.  
          Ex: *.shtml
            This is a convention and helps narrow down the footprint in case something goes wrong.

      Another option: IncludesNOEXEC
        Server-side includes are permitted, but the #exec cmd and #exec cgi are disabled.

        In general: to set up SSI's for a given directory.   
          Note: It's very important to lock down CGI scripts and SSI's to a particular directory.
            At this point, it's important to modify the permissions of this directory accordingly.
              Ex: If the use-case is correct, don't give users write access to this directory.

          <Directory "PATH_TO_DIR">
            Options Includes
          </Directory>

          You can change to

          <Directory "PATH_TO_DIR">
            Options IncludesNOEXEC
          </Directory>

          Note:
          <--#include virtual="..." -->
            Users may still use virtual includes (seen above) to execute CGI scripts if you have set up the ScriptAlias directive.


  Permissions for ServerRoot Directories
    Apache is started by the root user and then it spawns other processes to serve requests.  These additonal processes are spawned utilizing the apache account.
      T: ps -aux | grep httpd

    As the files in the ServerRoot can be run by the root process, it is very important to make sure that they have chmod 755 and are owned by the root user and group.
      If another user compromises the system and puts malicious code (or a sym link/s) in place of these files, the malicious instructions will be run as root.

      When I checked my httpd installation, the correct ServerRoot permissions were in place.  However, it would be wise to double check your installation.

    What about files/directories in the DocumentRoot?
      Root never executes files in the DocumentRoot (and by default doesn't create any files in there)

      Although this is the case, it's still prudent to follow the principle of least privilege.



  Denial of Service (DoS) Attacks
    Basic Idea
      Application servers typically allocate an operating system thread/process for each connection.  As a process is a relatively memory intensive resource, it's very easy to initiate DoS attacks.
        Older vs newer editions of Apache?  Does this hold now?

      Do a DoS lecture and go into more depth
        Have a broad nginx lecture (like this one) and have a section on preventing DoS attacks (and other items) in nginx.
          https://www.nginx.com/blog/http-keepalives-and-web-performance/

      "HTTP Heavy Lifting"

    Try to prevent responses to clients by typing up resources on the server.

    You can't prevent these attacks entirely, but you can do create mitigating factors.

    Create a multi-tier solution 
      In addition to the security tips outlined here, it's very important to have correct firewall configurations
        We will look at this in depth in another lecture.

        Many firewalls can be configured to restrict the number of simultaneous connections from any unique IP/network.
          This isn't effective against DDoS attacks (Distributed Denial of Service Attacks)

    Server Configs that can help if you're receiving a DoS attack (or are highly vulnerable to them)

      In general look at all directives that you utilize for their timeouts.  In particular, I'd also reference the ones below:
        
        KeepAliveTimeout directive
          Talk about apache concurrency vulnerabilities and SSL
            https://www.nginx.com/blog/http-keepalives-and-web-performance/
            You know that there's a DoS risk if a website is using apache and SSL (put this in ethical hacking notes)

          Number of seconds apache will wait before closing the connection.
            Thus, if no requests are sent in a certain time frame, the connection is "idle" and it is closed.

            Once the connection is established, the timeout value specified in the Timeout directive applies.
              It is important to know the differences between the directives.

          KeepAlive (On|Off) Directive
            You can turn KeepAlive on/off as well

            However, just like lowering the KeepAliveTimeout, there can be performance implications to this action.
              
              Without KeepAlive
                For every client request 
                    
                    Opening connection
                      Three-way TCP handshake
                      
                    Client sends an HTTP request
                    Client receives an HTTP response

                    Closing connection
                      Two-way handshake

            In a DoS attack, you must weigh the costs/benefits when changing keepAlives
              Pros: Concurrency portion of DoS in mitigated
              Cons: Extra overhead of connections being established







      
      RequestReadTimeout directive
        Sets a limit for the amount of time a client can take when finishing the request

      TimeOut Directive
        If receiving a DoS attack, one should look at this config first.
          Ex: The default is 1 minute, but if you're receiving a DoS attack, it might be wise to lower this to a few seconds.

        Be very careful, as this is an "aggregate" directive which affects multiple settings that CAN have adverse consequences.

        Has a larger footprint then the RequestReadTimeout directive.
          This directive sets a timeout for
            reading data from a client
              But what if a client trickles data very slowly?
                Hard to mitigate against this.

            When sending data to a client, the maximum time to wait for a acknowledgment of a packet (ACK) when the send buffer is full (client is "slow" to receive packets)

            Max waiting time for output from a CGI script
              If you have running CGI scripts, you need to make sure that your timeout isn't too low!  This might effect this setting!

            Max waiting time for output from a filtering process
              Filtering processes can include compression, etc.
                Make sure that your timeout value takes into account filters that are in place already!

            If you're utilizing Apache as a Proxy and have NOT set the ProxyTimeout directive
              This will set a timeout on proxy processes.
                See the mod_proxy documentation (anything the references the default timeout value) for the scope of the timeout.

        Limiting the size of requests
          LimitRequestBody directive
            Default is unlimited!
            How many bytes can be specified in the request body

            You can make this very granular and can tune it to the following levels
              server
              per-directory
              per-file
              per-location

          LimitRequestFields directive
            Default 100
              In PROD, rarely goes over 20
            Limiting the headers in HTTP requests

          LimitRequestFieldSize directive
            Limits the size of the HTTP request header allowed from the client. 

            Default 8.19 kb
          LimitRequestLine directive
            Limits the size of a clients HTTP request-line

            Request Line
              Consists of the HTTP method, URI, and the protocol version.

            This directive limits the size of the URI so check this against your use-case

          LimitXMLRequestBody directive
            Limits the max size of an XML-based request body.

            Default
              1 mb

          MaxRequestWorkers Directive
            Maximum number of connections that will be processed simultaneously.
              Make sure that this is set to a limit that your server can handle.
                Large enough to service requests, but not too large to drain too many resources and crash server.

            Any number over this directive's limit will normally be queued, up to a number based on the ListenBackLog directive.
              Need to make sure you set a limit on the ListenBackLog directive

          ListenBackLog Directive
            Sets a maximum limit on the MaxRequestWorkers Directive.

            If under a SYN flood attack (who's goal is to defeat this directive's backlog), it can be helpful to increase this limit.

            Syn flood attack is a type of DoS attack that involves the TCP's Three way handshake.  We will explore this in further detail in the DoSing lecture.

          Utilizing a threaded mpm
            May allow you to handle more simultaneous connections which can help in DoS situations

            MPM = Multi-Processing Module
              Responsible for binding to network ports on the machine, accepting requests, and dispatching children to handle the requests.

              Potential MPM Modules to explore to mitigate DoS risks

                event MPM
                  Designed to allow more requests to be served simultaneously by passing off some processing work to supporting threads, which frees up the main threads to work on new requests.

                  Nutshell
                    Utilizes asynchronous processing to avoid utilizing a thread for each connection.

                  Caveat
                    Isn't compatible with mod_ssl, which is responsible for SSL connections in Apache.  If you're utilizing this feature, it event MPM will revert to the worker MPM (another thread based approach)

                    Nutshell
                      Use event MPM if your architecture is compatable.

                      For Apache 2.4, look in
                      https://httpd.apache.org/docs/2.4/mod/event.html
                        Under 'Requirements'

            Additional security modules that can help with DoSing or other security measures
              modules.apache.org
                Tutorial: Show security category section.

                Make sure to look through code, etc.
