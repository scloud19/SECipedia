Types of enumeration
  Enumerating via GET requests (sometimes referred to as application page mapping) or POST (or other non GET request) (sometimes referred to as functional path mapping)
    Certain applications expose their functionality through different URLs
      While others can expose their functionality to a single url that gets hit with different parameters (via a POST request, etc.)

      It is often more fruitful to map via the functional paths (even if app page mapping "works") because these relationships are often more logical than directory structure
 
Look at "site map" for enumerating content.




Web Spidering

  Many spidering tools leverage robots.txt as a starting point
    Thus, this can be counterproductive to a websites security.

    Ex: If these routes aren't referenced anywhere in the site, a traditional web spider couldn't find them
      Z: But could google? What are the costs/benefits

  Work by requesting a web page, parsing it for links, (and doing so again recursively until nothing else is discovered.)

  Some tools can also
    HTML forms and submit these back to the application using various preset/random values.

    Parse client-side javascript to extract URLs


  Limitations:
    Per-page tokens
      The spider will usually fail to handle these properly by requesting pages out of their expected sequence.
        A web app can defend against this by canceling the session.


    Authentication mechanisms
      aka Need to spider the "logged in area"

      Some spiders can be configured with a session token, etc. but there are limitations
        Ex:
          The spider will request the logout link, which causes the session to break

          If the spider submits invalid input to a sensitive function, the app may defensively terminate the session


    Complicated Navigation Mechanisms
      Are often not handled properly by these tools
      
      Ex:
        Menus created dynamically by javascript

    Links buried within compiled client-side objects
      Ex: Flash/Java applets

    Input validation
      Multistage functionality often implements input validation
        This input validation might not accept the values that are submitted from the spider.
          Ex: submitting a test string in a zip code field.

      Typical spiders submit one test string into these fields (and are easily to spot)

    Urls are "unique"
      A spider can believe that one url is the same, and thus won't navigate to the same url again.  (This is so the spider doesn't go into an infinite loop).
        However, based on the context, sometimes a unique url can serve up completely different results (and thus, links)

      Ex: Forms based navigation
        A bank app may implement every user action via a POST request to /account.jsp

      Potential Solutions
        Burps Spider can be configured to see the true uniqueness of a form submission based on the POSTs param names/values.

    App's that cause a spider to go into an infinite loop
      Ex: Apps that contain params containing timers/random number seeds

User-Directed Spidering
  

  The user talks through the app in a normal way using a browser, while attempting to navigate through all of the apps functionality.
    As this is accomplished, the resulting traffic is passed through a tool combining an intercept proxy and spider.
      This tool builds a map of the app, incorporating all the URLs visited by the browser.  Additionally, the tool parses all the apps responses in the same way as a normal app-aware spider and updates the site map with the content and functionality that it discovers.

  Usually preferred to automated spidering
    Is more sophisticated and controlled.

    Helps in the aforementioned problems section of automated spidering

    Any dangerous functionality, such as a link that deletes a user, is parsed out of the app's responses and added to the site map.  However, the link isn't automatically followed.

  PenTest Steps
    Using burp as a proxy, browse the entire app normally, attempting to visit every link/url, submit every form, and proceed through all multistep functions to completion.  Try browsing with JS enabled/disabled, cookies disabled/enabled.  Many apps can handle various browser configs and you may reach different content/code paths within the app.

    Review the site map and identify any content that you did not browse manually and see how the spider found it (or how it enumerated it).
      Ex: in burp there's a "linked from".

    In the browser, navigate to this area and complete the first two steps until no new content is found.
      Another option is to tell the tool to actively spider the site using all of the already enumerated content as a starting point.
        For this to occur you need to identify any URLs that are dangerous or could break the applications session./

Finding hidden content
  
  Third-party components
    Many web apps incorporate third-party components for standard functionality (such as shopping carts, discussion forums, CMS, etc.)
      These are often installed to a fixed location relative to the web root or the apps starting directory

      There are automated tools that lend themselves naturally to this type of task
        See Wikto
          Ex: finding http://myapp.com/phpmyadmin/


  It's fairly common for applications to contain functionality that is not directly visible in the main content
    Ex: 
      Functions used in testing/debugging purposes that was never removed

      This can also occur when an application exposes different functionality to different roles of users (admin, anonymous, etc.)
        Spidering at one role may not find functionality exposed to another role (that's still 'open')

    Examples of hidden content
      Backup copies of live files.
        As the file extension might be different, you can potentially navigate to these files (i.e. via directory traversal, etc.) and view the source.
          This can inform attacks on the main page.

      Backup archives
        Can contain a full snapshot of files within (or outside of) the web root.

      New functionality that has been deployed to the server but isn't directly linked to the main application

      Default application functionality in an off-the-shelf application (or framework/template) that has been superficially hidden from the user but is still present on the server.

      Old versions of files that have not been removed from the server.
        Ex: Dynamic pages that contain vulnerabilities that have been fixed in the current version but can still be exploited in older versions

      Config (and include files) containing sensitive data.
        Ex: Database credentials

      Source files from which the live app has been compiled

      Comments/function names in the source code
        Ex: "Need to test this function"
            Auth function

      Log files
        Ex: Usernames, session tokens, URLs visited (for spidering), and actions performed.


      Tools for finding hidden content
        Burp Intruder
          Utilize Burp yIntruder to do a brute force enumeration over common directory names

      "Juicy" routes
        /admin
        /auth

      Technique
        Lets say you find content via burp intruder (acct registration page) that you don't find through spidering.  It would be wise to try to sign up for an account (as this might be "hidden" functionality).

        Most apps employ a certain naming scheme for their functionality, utilize this to fine tune your enumeration "master route/word list", etc.
          Ex: Are resource names camel case, all caps?

              If you see www.yoman.com/user/11
                This is a prime target for enumeration

          Steps to hone in on naming scheme and find hidden content
            1) Review the results of user-directed browsing and basic brute force enumeration.  Compile lists of the names of all subdirectories, file stems, file extensions.  Add to this list by the sub-steps below.  Do all combinations to try and brute force.  For example, in a given directory, request each file step combined with each file extension.  Try to request each directory name as a subdirectory of every known directory.

              Review this list to ID any naming schemes in use.  Ex: if you see an AddDocument.jsp, maybe you can try a RemoveDocument.jsp (and all other REST IDs: edit, delete, etc.)  Also, try Addxxx (AddUser, AddFile, etc.)
                Exs: Are the devs verbose (AddANewDocument.jsp), succinct (AddDoc.jsp), use abbreviations (AddDc.jsp), or cryptic (AddD.jsp)

              Does the naming scheme use numbers/dates?
                Ex: payReport2003.pdf
                It would be very easy to try the 2016 version.

              Review all client-side code to identify any clues about hidden server-side content
                Ex: 
                  HTML comments about protected or unlinked functions
                
                  HTML forms with disabled SUBMIT elements

                  References to server side include files
                    this may be publicly downloadable

                    can contain highly sensitive information such as DB connection strings, passwords, etc.

            2) Add to the lists of enumerated items any further potential names that you can deduce from the aforementioned steps.
              Also add to the file extension list common exts such as txt, bak, src, inc, old
                Add in extensions associated with the language in use (such as .java/.cs)

            3) Search for temp files that may have been created inadvertently by dev tools and file editors.
              Ex: .DS_Store
                contains a directory index in OS X

                  file.php~1
                    Temp file that is created when file.php is edited

                  *.tmp
                    Used by numerous software tools

            Note: Some of the aforementioned steps for finding hidden content (naming scheme) can be found through the Content Discovery feature of Burps Pro tools.  After you have manually mapped a portion of an apps content using your browser, you can select one or more branches of Burps site map and initiate a content discovery session on one of those branches.
              For more specific examples, see web_app_sec_resources.md


        It is difficult to fully leverage automated tools because the responses from the server can have different meanings (i.e. 200 might truly be a 200- Look at http_status_codes.md for me).
          Solution Steps:
            Make some manual requests for known valid and invalid resources, and identify how the server handles the invalid resources

            If you find that the application handles invalid resources in a non-standard manor (aka a 200 with a "file not found" page), configure intruder to highlight these results so they can be ignored.

            Use the site map generated through user-directed spidering as a basis for automated discovery of hidden content

            Use Burp Intruder (with a wordlist of common filenames)Make automated requests for common filenames/directories within each directory/path known to exist within the app.











