Basics
  As browser exts can make NON HTTP requests (that aren't seen in a typical proxy.. aka Burp), use a network sniffing tool to make sure that no other protocols are being leveraged.

Exs of attacks
  Online casino exts

Besides HTML forms, the other main method for capturing and submitting user data i through browser extensions.

Browser extensions help when speed is critical.
  Ex: Online trading platforms have a lot of logic in their extension code

Methods of input capture
  input forms
  OSs file system

Transparency 
  Developers are prone to assume that exts can't be circumvented as they're not as easily "readable"
    Thus, this is a fruitful target

Popular browser extension technologies
  Java applets
  Flash
  Silverlight

    Items in common (Java applets, Flash, Silverlight)
      Are compiled to an intermediate bytecode

      Execute within a VM that provides a sandboxed env

      May use remoting frameworks which can employ serialization to transmit complex data structures over HTTP


  Java Applets
    Run in the JVM
    Java Security Policy
      Applies the sandboxing

  Flash
    Flash objects run in the Flash virtual machine

    Can be used to fuel desktop application

    Implements Action Message Format (AFM) Serialization

  Silverlight
    Microsofts alternative to Flash

    Allows web apps to provide a scaled-down .NET experience within the browser (which is sandboxed)

    These apps can be written in any .NET-compliant language from C# to Python
      Most are written in C#

Problems Intercepting Traffic from Browser Exts
  Symptoms
    Browser Ext is communicating in a non HTTP-protocol
      This can't be handled using an intercepting proxy

      Solution
        Modify the network traffic by using a network sniffer or function-hooking tool
          Ex: Echo Mirage
            Can inject into a process and intercept calls to socket APIs
              This allows you to view/modify data before it's sent over the network.
    Requests made by browser exts are not being intercepted by the proxy (or are failing)
      This is usually due to issues with the exts handling of HTTP proxies and/or SSL.

      Solution
        Careful configuration of Burp

      Pot. Subproblems
        Ext may not honor the proxy config you have created
          This is because exts can issue their own HTTP requests (outside of browser APIs or ext framework)

          Solution
            Modify your CPUs host file to achieve the interception
            Configure your proxy to support invisible proxying and automatic redirection to the correct destination address.

        Ext isn't accepting the SSL cert being presented by your intercepting proxy
          If your proxy is using a generic self-signed cert (and you've configured the browser to accept it)
            The ext may still reject it.

              Reasons for rejection
                1) The ext does not pick up the browser's config for trusting certain certs

                2) The ext itself specifically blocks untrusted certs


              Circumvention 
                1,2
                  Configuring the proxy to use a master CA certificate
                    These certs are used to sign valid per-host certs for each site you visit.

                    Make sure to install this CA cert in your CPU's trusted cert store.
