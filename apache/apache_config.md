/etc/httpd/conf/httpd.conf

Common directives
  Listen 80
    the listening port

    By default, listens on all interfaces, but this can be changed.

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

  Apache bootstrap
    For all privledged ports (ports less than 1024) must be bound and used by root
      Thus, when apache starts, the initial process starts as root.

      After the initialization, apache spawns a number of child processes that wait for connections.  These processes run with the User and Group identity as seen below.

        Directive Name/ User or Group name
        User apache
        Group apache
          The apache group and user account were added when the httpd package was installed

        When serving files from apache, linux looks at the user and group identites and will allow/deny access to a given file

  LoadModule directive
    By default, apache loads a large number of modules.  What you should do, from a security perspective, is only load the modules you need.

  Multi-Process Settings
    To improve latency, apache maintains a pool of "spare" server processes.  Apache will spawn new processes if things get busy, and kill processes off if server load diminishes.

    These directives (below) shouldn't need to be tweaked.

    Directives:
      StartServers 8
        Number of child server processes created on startup.  As the number of processes is dynamically controlled depending on the load, there is usually little reason to adjust this parameter.
      MinSpareServers 5
      MaxSpareServers 20
      ServerLimit 256
      MaxClients 256

