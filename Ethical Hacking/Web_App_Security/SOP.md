Same Origin Policy
  Applied inconsistently across browsers, their versions, and even plugins.

  Basic idea
    Content received from one website is allowed to read and modify other content received from the same site.
      However, this content (aka Javascript) can't access content received from other sites.

      What does this prevent?
        If the SOP didn't exist...
          If a user browser to a malicious website, script code running on that site could access the data/functionality of other websites visited by the user.


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

Key items for consideration when using the SOP
  The following features of the SOP can lead to cross-domain attacks...
    
  A page residing on one domain can cause an arbitrary request to be made to another domain (Ex: submitting a form/loading an image).  But, it can't process the data returned from the request.
    Exploit: Send sensitive data as a parameter in a GET request

  A page residing on one domain can load a script from another domain and execute it.
    This is because scripts are assumed to contain code, rather than data, so cross-domain access should not lead to disclosure of any sensitive information

  A page residing on one domain can't read/modify the cookies or other DOM data belonging to another domain.





