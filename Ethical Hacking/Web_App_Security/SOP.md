Same Origin Policy
  Applied inconsistently across browsers, their versions, and even plugins.

  Basic idea
    Content received from one website is allowed to read and modify other content received from the same site.
      However, this content (aka Javascript) can't access content received from other sites.

      What does this prevent?
        If this SOP didn't exist...
          

Definition
  Essentially is a sandbox.

  Restricts resources from one origin interacting with other origins.
    Most importantly, the SOP prevents access to the DOM of other origins
     Ex: An attacker who bypasses the SOP can extend their control over the browser and extract data from other origins

  Was initially defined only for external resources, but now includes schemes such as:
    file://
    chrome://
    etc.

  Origin
    Consists of a hostname, scheme, and port.




