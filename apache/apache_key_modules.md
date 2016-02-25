Key monitoring modules
  mod_status
    Security issues
      If mod_status is loaded into the server, its capability is available in ALL configuration files (this includes per-directory files, ex: .htaccess)
        This may give unwanted access to sensitive information
    
    Troubleshooting/DoS considerations
      See apache_troubleshooting.md/mod_status

    Human readable form that mod status produces
      An HTML page that gives server stats
        Can be set to autorefresh
          Going off of Ex1:
            http://theman.com/server-status?refresh=SECONDS
              Where SECONDS is a variable
    Machine readable form that mod status produces
      Another stats page that can be leveraged in scripting, etc.
        Going off of Ex1:
          http://theman.com/server-status?auto

      Leverage this page for a "uptime" report that can be shown on the main page of the website

      Enabling Status Support
        Ex1:
          Only for browsers from theman.com
          
          <Location '/server-status'>
            SetHandler server-status
            Require host theman.com
          </Location>

          http://theman.com/server-status
