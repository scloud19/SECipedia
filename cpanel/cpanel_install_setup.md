Caveats
  Only works on publicly visible, static IPs.
    Add in elastic IP
  Only install on freshly-installed OS
To install on server
  cd /home && curl -o latest -L https://securedownloads.cpanel.net/latest && sh latest

Basic server setup
  WHMs "Basic cPanel & WHM Setup" interface
    Home >> Server Configuration >> Basic cPanel & WHM Setup
      If you can't figure out something through the the GUI
        https://documentation.cpanel.net/pages/viewpage.action?pageId=1507826
    Mirrors
      /etc/wwwacct.conf

    Do the setup though the GUI, it's much easier

  /var/cpanel/cpanel.config
    Houses configs for
      WHMs Tweak Settings interface
        Home >> Server Configuration >> Tweak Settings
      Other Misc settings
        If there are notable settings that aren't in the Tweak interface, i'll reference them below.
      If you can't figure out something through the GUI OR you need information about a setting that's NOT in TWEAK
        https://documentation.cpanel.net/display/ALD/The+cpanel.config+File
      /var/cpanel/cpanel.config DEFAULT values
         /usr/local/cpanel/etc/cpanel.config
      
      If you need more information about a setting that IS in tweak
        Recommented the READ THROUGH all of these BEFORE setting up tweak

        https://documentation.cpanel.net/display/ALD/Tweak+Settings+-+System
        
        https://documentation.cpanel.net/display/ALD/Tweak+Settings+-+Domains

        https://documentation.cpanel.net/display/ALD/Tweak+Settings+-+Redirection

        https://documentation.cpanel.net/display/ALD/Tweak+Settings+-+Stats+and+Logs

        https://documentation.cpanel.net/display/ALD/Tweak+Settings+-+Stats+Programs
        

    Notable Settings NOT in Tweak interface
      I will outline by the key name in cpanel.config

      access_log
        If more reference needed
          cpanel_logging.md/access log

        Specifies the location of the cPanel Access Log.  This contains general info about cPanel access requests

        Default: /usr/local/cpanel/logs/access_log

      adminuser
        Default: cpanel




