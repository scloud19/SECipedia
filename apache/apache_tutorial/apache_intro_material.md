TODO: Go back through all of my notes and make sure that I included everything.

Apache Version Used
  httpd 2.4 on a Linux box (I'm on Amazon Linux)
    Some of the paths that I reference might be different from your distro
      Amazon Linux is based on Red Hat, etc.
    Assumption that you can get the bare installation up an running

Apache Structure Overview
  We're going to be discussing modules, and directives a lot in this series.  So lets get an overview.

    Modular Nature
      Modules are the "powerhouses" that run various parts of apache.  Directives are used to configure these modules and are place in /etc/httpd/conf/httpd.conf

      Server binary contains these modules
        core
        prefork
        http_core
        mod_so

        If you build from source, you can customize which of these modules are included

      Although most of apache's features are loaded through "external" modules that are loaded dynamically
        /usr/lib64/httpd/modules

        LoadModule
          These modules can be loaded via the "LoadModule" directive in the configuration file

        If you go to the documentation https://httpd.apache.org/docs/
          httpd -v
            Find correct version for the docs

           If you find a given directive, you will see what module it maps to.
            Similarly, you can reference a module and see its associated directives.


Apache Configs Overview
  Because we're going to be covering some security implications of these configs, I thought that I'd do a broad overview.

  Tips for viewing file without all of the comments
  grep -v "^#" /etc/httpd/conf/httpd.conf | less
    Also state that this is the config file, etc

  Misc items in httpd.conf file
    Include conf.d/*.conf
      will load all of the .conf files in the conf.d directive
      Think of this as an 'include'

  Common directives
    Listen 80
      the listening port

      By default, listens on all network interfaces, but this can be changed.

      Apache Docs
        Ex: To make the server accept connections on port 80 for one interface, and port 8000 on another, use

        Listen 192.0.2.1:80
        Listen 192.0.2.5:8000

    DocumentRoot "/var/www/html"

      The directory out of which you will serve your
      documents. By default, all requests are taken from this directory, but symbolic links and aliases may be used to point to other locations.

        Ex: http://securingthestack.com/isDaBomb.html
          Resolves to
            /var/www/html/isDaBomb.html

        Think of the potential security implications of symbolic links and how they can take you outside of the document root.


    LoadModule directive
      By default, apache loads a large number of modules.  What you should do, from a security perspective, is only load the modules you need.

    Multi-Process Settings
      To improve latency, apache maintains a pool of "spare" server processes.  Apache will spawn new processes if things get busy, and kill processes off if server load diminishes.

      These directives (below) shouldn't need to be tweake

        Container directives
          Use XML-style opening/closing tags

          Restrict the scope the of the directives they contain.

          Ex:
            <Directory "/var/www">
              AllowOverride None
              Options None
            </Directory>

            These directives only are applied for the /var/www directory.

            Other containers
              Note: There are closely related directives that match on a regex instead of a string
              
              <Location /admin>
                Matches against the path of a URL
                Ex: http://securingthestack.com/admin

              <Files "*.gif">
                Wildcard/RegEX matching

              <VirtualHost *:80>
                For any virtual host (with any IP) that uses port 80

      Directives:
        The following directive will only work if the 
        prefork module is loaded.
      
      <IfModule prefork.c>
        StartServers 8
          Number of child server processes created on startup.  As the number of processes is dynamically controlled depending on the load, there is usually little reason to adjust this parameter.
        MinSpareServers 5
          Idle servers that can be added due to load
        MaxSpareServers 20
         Idle servers that can be added due to load
        ServerLimit 256

        MaxClients 256
          Maximum number of connections that will be processed simultaneously.  For additional requests that exceed this number, they will be queued.
            Remember HTTP is stateless
      <IfModule>

        Other directives
          ServerRoot '/etc/httpd'
            Where the log files and configuration file are.
            The files/directories that are referenced in the conf files will be relative to this directory.

          DirectoryIndex index.html
            What file apache will serve if a directory is requested

Apache bootstrap and associated privileges

  For all privledged ports (ports less than 1024) must be bound and used by root
    Thus, when apache starts, the initial process starts as root.

    After the initialization, apache spawns a number of child processes that wait for connections.  These processes run with the User and Group identity as seen below.

      Directive Name/ User or Group name
      User apache
      Group apache
        The apache group and user account were added when the httpd package was installed

      When serving files from apache, linux looks at the user and group identites and will allow/deny access to a given file


Apache Authentication/Authorization
      I did a LOT of work documenting the relevant apache authentication mechanisms and in the end, I decided to scrap my work because in my opinion, for web application development, it makes more sense to go with more traditional auth routes (OAuth, OpenID, etc.)  
        With these routes, one can leverage "Sign On through Google", etc. which is a HUGE UX advantage.  Also, Apache's basic authentication mechanism, based on HTTP's "Basic Access Authentication", pops up an 1990's web browser authentication dialog box to perform authentication.
          This isn't good for UX purposes

        Apache does have a form based authentication module, but I'd rather use a library that has TONS of extensibility and users (thus more stack overflow answers/blog posts, etc.)  Thus I'd suggest another auth route.  In the future, I will be doing an follow up lecture to look at the pros/cons/differences between the "big players" in web authentication (Oauth, openID, etc.) and the different types of auth schemes out there (kerberos, etc.)

Logs
  Tutorial: Look at enumeration.pic and talk about how to blacklist IP from apache.
    Talk about the importance of looking in logs
    Reverse IP lookup for: 180.129.156.3
      IP range is from the china
