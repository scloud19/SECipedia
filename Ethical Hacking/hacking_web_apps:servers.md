Software/Websites
  w3dt.net/tools/httprecon
    Used for server fingerprinting
      aka: Makes requests to a webserver and looks at responses.  If the responses match a pattern, you can deduce the webserver, etc. being everything.

      Be careful, because these tools aren't 100% acurate
        Versions change all of the time, and thus the fingerprints need to constantly be updated.  Is this happening from the author? 

  passivetotal.org
    Enter URL
      Click the IP address that the URL resolves to
        Look at all of the URLs that are tied to that address
          These are the results of a reverse DNS lookup
            aka: Taking an IP address and seeing what other hostnames are associated with it

        From this, you might find the admin portal of the site, etc.
          You can probe this for vulnerabilities
            Compromising one resource on a web server can often get you access to other resources
      
      Select the WHOIS
        You can click on the email address associated with the registration, etc.  This will do a reverse lookup.
          From here, you can see other webites that are associated with an individual and look for exploits
            If one website has a vulnerability by one person, there are probably others.

  Netsparker
    crawling
      Creating a site-map of the target
      Good to see overarching structure of the site and look for weaknesses
        Ex: we can see a folder called "admin" or "secret", etc.

      Look for .zip or other archive files
        Ex: backup.zip, database.zip, etc. 

  HTTrack/Sitesucker
    Mirroring Websites

    Very helpful to grab a whole copy of the website for RECON.  In the future, when you're doing things, this doesnt raise as many flags because many of your requests are local.


    After you download website, do global searches for "juicy" topics
      EX: password
          cookie


Server Recon
  Use this recon to find vulnerabilities in given software, etc.

  Chrome/Network Tab/NAME_OF_DOMAIN
    Response Headers
      Server

DNS Registery
  If you hack this, you can reroute traffic

Insecure Webserver Configs
  If error messages are on
    Error based SQL injection

    "But the exceptions that are shown aren't 'bad' "
      BUT... when paired with error based sql injection, it becomes very bad
