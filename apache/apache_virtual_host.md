Virtual Hosting
  Virtual hosting is a method for hosting multiple domain names on a single server (or pool of servers)
    Each domain has a seperate handling

  This can exist outside of webservers
    Ex: ftp

  Apache allows for virtual hosting

  2 ways to do virtual hosting
    Name-Based Virtual Hosting
      Different DNS enteries that resolve to the same IP
        Ex:
          daMAN.securingthestack.com --> 111.111.111.111
          dawoMAN.securingthestack.com --> 111.111.111.111
            When Apache recieves the request, it sees the "Host" header in the HTTP Rquest and maps that to the correct virtual host.


        Virtual host "daMAN"
          is correlated to a DocumentRoot directive
            Ex: /var/www/html/daMAN

        Virtual host "dawoMAN"
          is correlated to a DocumentRoot directive
            Ex: /var/www/html/dawoMAN

      Ex: Enabling virtual hosting in httpd.conf
        # Start virtual hosting on port 80
        NameVirtualHost *:80

        # Requests that don't map to any virtual host are
        # served via the first entry.
        # This shouldn't usually be an issue if you set up 
        # your DNS to IP resolution correctly.

        <VirtualHost *:80>
          ServerName daMAN.securingthestack.com
          DocumentRoot /var/www/html/daMAN
          ErrorLog /var/log/httpd/daMAN/error_log
          TransferLog /var/log/httpd/daMAN/access_log
        </VirtualHost>

        <VirtualHost *:80>
          ServerName dawoMAN.securingthestack.com
          DocumentRoot /var/www/html/dawoMAN
          ErrorLog /var/log/httpd/dawoMAN/error_log
          TransferLog /var/log/httpd/dawoMAN/access_log
        </VirtualHost>

        # You can bind almost any directive to the virtualHost container

        # If you have a current site, make sure that it's
        # entered as a vhost

        Tutorial: Look at enumeration.pic and talk about how to blacklist IP from apache.
          Talk about the importance of looking in logs
          Reverse IP lookup for: 180.129.156.3
            IP range is from the china

        Tutorial:
          For name-based virtual hosting in PROD, we would have seperate DNS entries for our domains and would point them to the same IP address.  But, to mimic this in our local ENV.

            /etc/hosts/
              127.0.0.1 localhost
              127.0.0.1 daMAN.securingthestack.com
              127.0.0.1 dawoMAN.securingthestack.com
              




