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
      If you can't figure out something through the GUI
        https://documentation.cpanel.net/display/ALD/The+cpanel.config+File
      




