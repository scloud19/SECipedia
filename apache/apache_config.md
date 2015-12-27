/etc/httpd/conf/httpd.conf


Tips for viewing file without all of the comments
grep -v "^#" /etc/httpd/conf/httpd.conf | less

Misc items in httpd.conf file
  Include conf.d/*.conf
    will load all of the .conf files in the conf.d directive

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
