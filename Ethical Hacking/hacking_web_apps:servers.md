Look at apache_security_tips.md for bad defaults on apache servers.  This bad defaults can be used in DoSing.  There is a lot of other good information in these notes as well (from a security perspective)

Look in Google Hacking for related searches

Software/Websites
  shodan.io
    Can search for vulnerable webservers in the search bar.
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

Error logs
  If you can access them, search for server variables

  Additionally, look for "cookie" as you can utilize this for session hijacking.
    Potentially do google search for common cookie strings "HTTP_COOKIE", etc. to find error logs and other items.

Insecure SSL/TLS Configs
  ssllabs.com/ssltest to see a domains SSL config is correct.
    Usually PaaS servers will be more secure (and recieve better grades) but IaaS won't be.
      The middleware/ssl implementation is being managed for you

  If a website supports the following it is insecure
    SSL 3
      Last version of SSL protocol that was used before TLS

      SSL effectively died in 2014 with the Poodle vulnerability
    SHA1
      considered insecure against well-funded opponents
    
    RC4 cipher
      Aside: This cipher is behind the WEP vulnerability
    Forward secrecy support

    POODLE vulnerability
      Both in SSL 3 and TLS

Insecure Webserver Configs
  In general, need to follow principle of least privilege

  Only give information in the response headers that's absolutely necessary.
    Don't reveal the "server" header, etc.
  Make sure that there isn't ANY backups that are being stored on the website itself

  Werkzeug
    shodan.io
      search for wekzeug

    Python debugging library for HTTP connections

    It can allow code to be executed from within the browser

    Sometimes this isn't DISABLED in prod 

  domain.com/elmah
    Very popular ASP.NET error logging module.  Can sometimes get to through this URL
      Ex Google Search: inurl:elmah.axd filetype:axd "Error log for"
        "Error log for" comes in the elmah error log file



  If error messages are on
    Error based SQL injection

    "But the exceptions that are shown aren't 'bad' "
      BUT... when paired with error based sql injection, it becomes very bad
