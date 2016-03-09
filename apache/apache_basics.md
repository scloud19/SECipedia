Definitions
  MPM- Multi-Processing Module
  ! - Apache config

Apache
  Install Apache on AWS Linux
    Put in bootup script
      You don't need to put sudo infront of these commands because the script is run as root

      yum update

      Start by searching Apache docs for current version

      yum search httpd
        Find one that matches the current version

      yum info httpd
        To double check version number

    
      yum install -y PACKAGE_NAME
      service httpd start
        Starts the service immediately
      chkconfig httpd on
        Makes sure that the service will be started on reboot

    See if everything started up correctly internally
      netstat -ant | grep 80

    To make sure that our security group is working correctly
      Go to EC2 IP in URL

    If problems
      /var/log/httpd/error_log


  Apache Structure
    Modular Nature
      Modules are the "powerhouses" that run various parts of apache.  

      Directives
         are used to configure these modules and are place in /etc/httpd/conf/httpd.conf

         Logic
           Inside of Modules/Directives there is access to an Apache "API" for logic.
            There are certain variables, conditional statements (if, etc), comparison operators and functions that you can leverage inside of directives/modules as needed.

              Notable binary operators
                -ipmatch
                  IP address matches address/netmask

              Notable unary operators
                Unary operators take one argument
                -d
                  The argument is treated as a filename.
                  Returns true if the file exists and is a directory

                  There is a bunch of other operators that deal with the filesystem (in a similar way)
                    See: Unary Operators
                      http://httpd.apache.org/docs/current/expr.html

              Notable Functions
                req, http
                  Get HTTP request header; header names may be added to the Vary header, see below  
                  
                  CURRENTLY AT
                    Functions
                      http://httpd.apache.org/docs/current/expr.html
                      Understaing the vary header
                        http://stackoverflow.com/questions/1975416/what-is-the-function-of-the-vary-accept-http-header

                req_novary  Same as req, but header names will not be added to the Vary header  
                resp  Get HTTP response header  
                reqenv  Lookup request environment variable (as a shortcut, v can be used too to access variables). 
                osenv Lookup operating system environment variable  
                note  Lookup request note 
                env Return first match of note, reqenv, osenv 
                tolower Convert string to lower case  
                toupper Convert string to upper case  
                escape  Escape special characters in %hex encoding  
                unescape  Unescape %hex encoded string, leaving encoded slashes alone; return empty string if %00 is found  
                base64  Encode the string using base64 encoding 
                unbase64  Decode base64 encoded string, return truncated string if 0x00 is found  
                md5 Hash the string using MD5, then encode the hash with hexadecimal encoding 
                sha1  Hash the string using SHA1, then encode the hash with hexadecimal encoding  
                file  Read contents from a file (including line endings, when present)  yes
                filesize  Return size of a file (or 0 if file does not exist or is not regular file)

              Variables
                %{VAR_NAME}
                  Note that the availability of certain variables might depend on the phase of the request processing in which it's evaluated.
                    Ex: An expression used in an <If> directive is evaluated before auth is done.
                      Thus {REMOTE_USER} will not be set

                that one has access to
                  Request headers variables
                    HTTP_ACCEPT
                    HTTP_COOKIE
                    HTTP_FORWARD
                    HTTP_HOST
                    HTTP_PROXY_CONNECTION
                    HTTP_REFERER
                    HTTP_USER_AGENT
                    (Note: Values of other headers can be obtained with req function )

                  Other request related vars
                    REQUEST_METHOD
                      The HTTP method of the incoming request
                    REQUEST_SCHEME
                      The scheme part of the request's URL
                    REQUEST_URI
                      The path part of the request's URI
                    REQUEST_FILENAME
                      The full local filesystem path to the file or script matching the request, if this has already been determined by the server at the time REQUEST_FILENAME is referenced. Otherwise, such as when used in virtual host context, the same value as REQUEST_URI
                    LAST_MODIFIED
                      The date and time of last modification of the file in the format 20101231235959, if this has already been determined by the server at the time LAST_MODIFIED is referenced.
                    SCRIPT_USER
                      The user name of the owner of the script.
                    SCRIPT_GROUP
                      The group name of the group of the script.
                    PATH_INFO
                      The trailing path name information, see AcceptPathInfo
                        PATH_INFO EX:
                          Assume the location /test/ points to a directory that contains only the single file here.html. Then requests for /test/here.html/more and /test/nothere.html/more both collect /more as PATH_INFO.
                    QUERY_STRING
                      The query string of the current request
                    IS_SUBREQ
                      "true" if the current request is a subrequest, "false" otherwise
                    THE_REQUEST
                      The complete request line (e.g., "GET /index.html HTTP/1.1")
                    REMOTE_ADDR
                      The IP address of the remote host
                    REMOTE_HOST
                      The host name of the remote host
                    REMOTE_USER
                      The name of the authenticated user, if any (not available during <If >)
                    REMOTE_IDENT
                      The user name set by mod_ident
                    SERVER_NAME
                      The ServerName of the current vhost
                    SERVER_PORT
                      The server port of the current vhost, see ServerName
                    SERVER_ADMIN
                      The ServerAdmin of the current vhost
                    SERVER_PROTOCOL
                      The protocol used by the request
                    DOCUMENT_ROOT
                      The DocumentRoot of the current vhost
                    AUTH_TYPE
                      The configured AuthType (e.g. "basic")
                    CONTENT_TYPE
                      The content type of the response (not available during <If >)
                    HANDLER
                      The name of the handler creating the response
                    HTTPS
                      "on" if the request uses https, "off" otherwise
                    IPV6
                      "on" if the connection uses IPv6, "off" otherwise
                    REQUEST_STATUS
                      The HTTP error status of the request (not available during <If >)
                    REQUEST_LOG_ID
                      The error log id of the request (see ErrorLogFormat)
                    CONN_LOG_ID
                      The error log id of the connection (see ErrorLogFormat)
                    CONN_REMOTE_ADDR
                      The peer IP address of the connection (see the mod_remoteip module)
                    CONTEXT_PREFIX  
                    CONTEXT_DOCUMENT_ROOT 
                  
                  Misc variables
                    TIME_YEAR
                      The current year (e.g. 2010)
                    TIME_MON
                      The current month (1, ..., 12)
                    TIME_DAY
                      The current day of the month
                    TIME_HOUR
                      The hour part of the current time (0, ..., 23)
                    TIME_MIN
                      The minute part of the current time
                    TIME_SEC
                      The second part of the current time
                    TIME_WDAY
                      The day of the week (starting with 0 for Sunday)
                    TIME
                      The date and time in the format 20101231235959
                    SERVER_SOFTWARE
                      The server version string
                    API_VERSION
                      The date of the API version (module magic number)

                



              



      Server binary contains these modules
        core
        prefork
        http_core
        mod_so

        If you build from source, you can customize which of these modules are includes

      Although most of apache's features are loaded through "external" modules that are loaded dynamically
        /usr/lib64/httpd/modules

        LoadModule
          These modules can be loaded via the "LoadModule" directive in the configuration file

        If you go to the documentation https://httpd.apache.org/docs/
          httpd -v
            Find correct version for the docs

           If you find a given directive, you will see what module it maps to.
            Similarly, you can reference a module and see its associated directives.


  Tutorial: Segway to apache_config.md

  Creating Content
    Adding our own index.html
      cd /var/www/html
      sudo su
      echo hello world > index.html
      chown -R apache:apache *
      # apache owner/group
      exit

    Tutorial:
      Segway into apache_virtual_hosting.md
      
    Apache Authentication/Authorization
      I did a LOT of work documenting the relevant apache authentication mechanisms and in the end, I decided to scrap my work because in my opinion, for web application development, it makes more sense to go with more traditional auth routes (OAuth, OpenID, etc.)  
        With these routes, one can leverage "Sign On through Google", etc. which is a HUGE UX advantage.  Also, Apache's basic authentication mechanism, based on HTTP's "Basic Access Authentication", pops up an 1990's web browser authentication dialog box to perform authentication.
          This isn't good for UX purposes

        Apache does have a form based authentication module, but I'd rather use a library that has TONS of extensibility and users (thus more stack overflow answers/blog posts, etc.)  Thus I'd suggest another auth route.  In the future, I will be doing an follow up lecture to look at the pros/cons/differences between the "big players" in web authentication (Oauth, openID, etc.) and the different types of auth schemes out there (kerberos, etc.)

    apachectl
      Recommended way to run httpd
        All arguments are automatically passed to httpd

        This sets certain ENV variables that are necessary on some operating systems.

        Will pass through any CLI args

      Ever presents args
        Simply add them into the ctl script

        You can watch how signals/or other items interact with apache by
          Ex: tail -f /usr/local/apache2/logs/error_log

        Additional Args: start, restart, stop, graceful
          Must utilize -k to invoke
          
          apachectl -k stop
            This will send TERM to the parent
              The parent will immediately attempt to kill off its children.

          apachectl -k graceful
            Will send SIG USR1 to the parent

            Before issuing command
              You need to check the semantics of the config files, etc.
                Try starting httpd as non-root
                  If there are no errors, it will attempt to open its sockets and logs, but it will fail 
                    Failures
                      non-root
                      already bound ports
                    Config errors
                      Any failures that aren't listed above, thus you should make changes to configs before graceful restart

            mod_status
              Server stats
                When this signal is sent, the server stats are NOT set to 0
              G
                Indicates the children which are still serving requests that were started before the graceful restart

            Process
              Causes the parent process to advise the children to exit after their current request
                OR to immediately exit if they're not serving anything
              Parent re-reads its config files and re-opens its log files, as each child dies off the parent replaces it with a child from the new generation of the configuration and the child begins serving new requests immediately.

            This will always respect the process control directive of the MPMs
              So, the number of processes and threads available for serving clients will be maintained throughout the restart process

            Logging
              To rotate the logs after sending USR1, use a suitable delay
                Maybe set a 15 minute delay
                  Look at your traffic patterns to estimate when all of the children will "reset" and give yourself some padding (maybe 5 mins)

          apachectl -k graceful-stop
            SIG:
              WINCH

            Process

            1) Causes the parent process to advise the children to exit after their current request finishes
            2) Parent will then remove its PidFile and cease listening on all ports
              The parent will continue to run, and monitor children that are still handling requests

              Once all the children have exited (or GracefulShutdownTimeout has been reached), the parent will exit.
                If GracefulShutdownTimeout is met, a TERM signal will immediately be sent to (and terminate) all children         

            In a graceful state, A TERM signal will immediately terminate the parent process and all children.
              As the parent's PidFile will have been removed, you can't send this via apachectl/httpd

            Gracefully upgrading httpd in PROD
              graceful-stop allows you to run multiple identically configured instances of httpd concurrently.
                This can be leveraged for live upgrading httpd
                Ex: Gracefully stopping one httpd and concurrent starting the new httpd
                  This needs to be double checked before actually doing this in live ENV

                WARNING
                  Can cause deadlocks and race conditions with certain configs
                    Config directives/third-party module/persistent CGI
                      If any of these utilizes a lock file or state file, we need to make sure that that multiple instances of httpd do not clobber each others files.
                        Exceptions:
                          These files contain the server PID, so they should coexist with multiple Apaches totally fine
                            Mutex lock file
                            ScriptSock (unix socket file)
                  Other potential race conditions
                    rotatelogs
                      Multiple httpds that are attempting to rotate the same logfiles at the same time may destroy each other's files


            
    Apache Starting Process
      Runs as a daemon that executes continuously in the background to handle requests

      Privileged port
        If the Listen config is set to port 80
          One must have root to start apache

      Process
        User invokes httpd
          apachectl
            Recommended invocation is through the apachectl script


        Preliminary activities
          Reads httpd.conf
          Opening log files
        httpd launches several child processes
          These handle the request/response cycle from clients
          These processes run as a less privileged user
            This is controlled by the selected MPM

      Errors during startup
        Sent to console or the ErrorLog

      Misc
        Start on boot
          /etc/rc.local
            Add apachectl call
            This will start apache as root