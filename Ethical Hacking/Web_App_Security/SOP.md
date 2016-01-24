Same Origin Policy
  Applied inconsistently across browsers, their versions, and even plugins.

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




