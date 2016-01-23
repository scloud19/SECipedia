Base Assumption
  The browser will get compromised and come under the control of the attacker.

Sandboxing
  Attempts to encapsulate a high-probability area of attack.  Which allows for an increased focus on a smaller attack surface.  
    In a way, this provides a good risk-vs-reward investment of resources for the browser security team.

  A sandbox can be applied at many levels.
    
    Kernel level
      Separate one user from another.

    Hardware level
      Achieves privilege separation between user space and kernel space.

    Browser level
      The highest-level sandbox possible for a user-space program.

      Represents the barrier between the privileges given to the browser by the operating system, and the privileges of a subprocess running within the browser.

      To completely compromise the browser, you need to take at least two steps
        1) Find a vulnerability in the browser functionality.
        2) Break through the sandbox (aka sandbox bypass)
            These types of vulnerabilities are normally of the compiled code variety and attempt to subvert the functionality of the running process.
      Used browser sandboxing strategies
        Open up every website in a separate process
          This makes it different for one malicious website to cause further impacts against other currently visited sites (or the OS itself.)

          This level of sandboxing also applies to plugins and extensions
            Ex:
              Separate processing for PDF rendering, etc.

        IFrame Sandboxing
          IFrames can be used as a mechanism to include potentially untrusted content from cross-origin resources, and in some cases untrusted content from same-origin ones.
            Ex: Facebook's social media widget.

          Sandboxed IFrames is a method to include an HTML5 attribute that adds additional restrictions to the inline frame.
            These restrictions include preventing the use of forms, stopping script execution, not allowing top-navigation, and trapping the iFrame with an origin.

            These restrictions extend from each parent frame, ensuring that any nested iFrames automatically inherit the sandbox methodology.



