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


        Virtual host "daMAN"
          has a DocumentRoot directive
            Ex: /var/www/html/daMAN

        Virtual host "dawoMAN"
          has a DocumentRoot directive
            Ex: /var/www/html/dawoMAN




