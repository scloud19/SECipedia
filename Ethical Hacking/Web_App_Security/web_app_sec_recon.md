IDing Server-Side Technologies
CURRENTLY_AT
  Remember, items such as the Server header, etc. can be falsified by the sysadmin.

  Banner Grabbing
    Server header
      Remember that different portions of the app may use different servers, so different server headers may be received depending on the different part of the app that is utilized.

    Additionally, you may find server info in
      Custom HTTP headers
      Templates used to build HTML pages
      query string params

  File Extensions
    asp - Microsoft Active Server Pages
    aspx - Microsoft ASP.NET
    jsp - Java Server Pages
    cfm - Cold Fusion
    php - Php
    d2w - WebSphere
    pl - Perl
    py - Python
    dll - Compiled native code (C/C++)
    nsf/ntf - Lotus Domino

  URL Signatures Mappings
    Vignette CMS
      https://wahh-app/news/0,,2-421206,00.html
        Notice the comma-separated numbers

  Directory Name mappings
    servlet - Java servlet
    pls - Oracle Application Server PL/SQL gateway
    cfdocs/cfide - Cold Fusion
    SilverStream - SilverStream web server
    WebObjects/ {function_name}.woa - Apple WebObjects
    rails - Ruby on Rails

  Session Token Mappings
    JSESSIONID - Java Platform
    ASPSESSIONID - Microsoft IIS Server
    ASP.NET_SessionId - ASP.NET
    CFID/CFTOKEN - Cold Fusion
    PHPSESSID - PHP




    To see if a web server is of a particular type, try requesting an invalid file with a given file extension (ex aspx)
      If doing yo.com/doesntexist.html
        Returns generic "not found" message
      If doing yo.com/doesntexist.aspx
        Get a ASP.NET error message


      This occurs because many web servers map specific files extensions to different executables on the server
        Each component may handle errors (or '404s') differently


Web Server Vulnerabilities
  Look at the server layer for vulnerabilities that could allow for directories to be exposed

Spidering
  Done through a tool

  Following all links in a website and figuring out the underlying site structure

  Tools: Netsparker


Forced Browsing
  Will try to hit urls that aren't found through traditional spidering

  Tools: Burp Suite

HTML Source
  By looking at the HTML source, you can often view the validations that are occurring.
    Ex: All passwords need to have a max-length of 10 chars and can only be alphanumeric.
      This can easily inform a brute force attack

  HTML source also has a lot of other goodies, so it's important to throughly review it.
    Ex:
      The backend architecture of the app by reviewing the URL structures, etc.

Using Public Information
  An app that you're surveying may contain content and functionality that is not presently linked from the main content but has been linked in the past.
    

    To find this information
      Search Engines
        Exs: Google, Yahoo, MSN

        Also look for cached copies of the content

        View google_hacking for more info.

      Web Archives
        Ex: WayBack Machine (archive.org)

      Internet Forums
        Ex: stackoverflow.com,etc.

        Look up developers from a company on linkedin and then look for those developers on stackoverflow, etc.
          Additional ways to find people
            Names within the HTML source code, names found in the contact information section of the main website

        Usually, these forum posts contain source code, technologies in use, etc.


      These repos also contain references to content that is linked from third-party sites, but isn't immediately visible in the target application itself.
        Ex: Some applications contain restricted functionality for use by their business partners.  These partners may disclose the existence of the functionality in] ways that the application itself does not.

      Technique

Analyzing the Application
  Keys areas
    Core functionality
      The actions that can be used (when the app is used as intended)

    Peripheral app behavior
      Off-site links, error messages, admin/logging functions, use of redirects.

    Core security mechanisms
      Management of session state
      access controls
      auth mechanisms/supporting logic
        Ex: user registration, password change, acct. recovery

    User input
      All of the different locations at which the app processes user-supplied input
        Ex: every URL, query string param, item of POST data, cookies, HTTP headers
          Headers of importance
            User-Agent, Referer, Accept, Accept-Language, Host

    Tech employed on the server side (including static/dynamic pages), types of req. employed, use of SSL, interaction with databases (interaction with email systems, and other backend components.)


      