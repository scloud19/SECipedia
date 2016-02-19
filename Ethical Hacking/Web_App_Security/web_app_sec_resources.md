Good resources
  Bytecode obfuscators
    Java
      Jode
  vulnerability databases
    osvdb.org
      Check any third-party code against this db for vulnerabilities

  Web server fingerprinting
    Httprecon

  Vulnerability Scanning
    Netsparker
      Also do spidering

  Web Spidering Tools
    Burp, WebScarab, Zed Attack Proxy, CAT

    User directed spidering
      Burpe, WebScarab

    Finding hidden content
      Burp Intruder
        Enumerate over common directory names

      Wikto
        Finding resources that are installed on the webserver via enumeration
        Ex: domain.com/phpadmin

      Burp's Content Discovery Feature
          Burp uses the following techniques when attempting to discover new content (once new content is found, these steps or then carried out recursively):
            Brute force using built-in lists of common file and directory names

           Dynamic generation of wordlists based on resource names observed within the target application

           Extrapolation of resource names containing numbers and dates

            Testing for alternative file extensions on identified resources

            Spidering from discovered content

            Automatic fingerprinting of valid and invalid responses to reduce false positives

        OWASP's DirBuster
          Includes a large list of directory names what have been found in the wild.


  Suites of Pen testing tools
    Burp Suite
      
    OWasp ZAP

  Decompilation Tools
    Java
      www.varaneckas.com/jad

    Flash Tools
      Flash bytecode can be decompiled into ActionScript source code
        Although, a more fruitful alternative is to disassemble the bytecode into a human-readable form (without actually fully decompiling it)

      Tools:
        Flasm - www.nowrap.de/flasm
        Flare - www.nowrap.de/flare
        SWFScan — www.hp.com/go/swfscan
          Works for Actionscript 2 and 3

    Silverlight
      .NET reflector
        www.red-gate.com/products/dotnet-development/reflector/