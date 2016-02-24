Definitions
  MPM- Multi-Processing Module

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
      Modules are the "powerhouses" that run various parts of apache.  Directives are used to configure these modules and are place in /etc/httpd/conf/httpd.conf

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

            Process
              Causes the parent process to advise the children to exit after their current request
                OR to immediately exit if they're not serving anything
              Parent re-reads its config files and re-opens its log files, as each child dies off the parent replaces it with a child from the new generation of the configuration and the child begins serving new requests immediately.

            This will always respect the process control directive of the MPMs
              So, the number of processes and threads available for serving clients will be maintained throughout the restart process




            Once you have sent 
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