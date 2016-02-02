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
    Complicated Navigation Mechanisms
      Are often not handled properly by these tools
      
      Ex:
        Menus created dynamically by javascript

        

