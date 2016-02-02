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




