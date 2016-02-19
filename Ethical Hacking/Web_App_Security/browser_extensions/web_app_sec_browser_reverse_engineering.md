Most thorough method of attacking a browser ext
  Decompile the object, perform a code review, potentially modifiy the code to change the exts behavior, and recompile.

Certain app exts are compiled into bytecode (as seen in basics)
  Bytecode
    Platform independent binary representation that can be executed by a relevant interpreter (JVM, Flash Player, etc.)
      Each ext technology uses its own bytecode format

    The nature of bytecode means that it's theoretically possible to decompile the bytecode into something resembling the original source.
      Countermeasures
        Certain defensive actions by developers can cause the decompiler to fail, or make the code very difficult to follow and interpret.

Downloading the bytecode
  Find the file/url from the HTML source
    Paste the URL into the address bar, and the browser will prompt you to save the bytecode file into your local file system.

  Ex:
    Java applets
      Usually loaded found in <applet>
        <applet code="blah.class” codebase=”/scripts” id=”blahApplet”></applet>
    Others types can be usually found in <object>

  Sometimes, the original file may be hidden/loaded via a wrapper object that's provided by a certain browser ext framework.
    Workaround
      Review the proxy history after the browser has been loaded
        If you still can't find the bytecode file/request
          Turn off any file filter in the proxy (sometimes css, images, etc. are filtered out)

          Caching
            Browsers can aggressively cache the downloaded bytecode
              Even doing a full page refresh might not cause a re-request of the image

              Solution
                Clear the browser cache, restart all browsers


Decompiling the Bytecode
  1) rename to .zip to easily unpack
  The bytecode is usually distributed in a single file package
    This may need to be unpacked to get the individual files

    .jar files
      Java archive files
      Normally house the java applet byte code
      1) above
    .xap
      Silverlight objects package ext
      1) above
    flash files
      Are packaged as .swf files and don't require any unpacking before you use the decompiler

  
  Bytecode files
    Look for these exts which house the physical bytecode
      After these are recovered, one must still decompile

    java
      .class files

    silverlight
      .dll files

  Decompilation Process
    Java Tools
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

Reverse Engineering Techniques
  Common steps
    1) Review the source to understand how the component works and what functionality it contains
      Items to look for
        Input validation or security logic
          Obfuscation/encryption routines being used to wrap user data
      
        Hidden functionality that is not visible in the UI
          Can you unlock it?

        References to server-side functionality that wasn't previously IDed

        After reviewing the source, you may see that you can simply adulterate the javascript that interacts with the component (and then achieve your aims in that fashion)
          ID all of the exts public methods that can be invoked directly from the JS
            Look at the way in which the params to those methods are handled.

            'Secret methods'
              Many times, there are more methods than are physically called from the application pages

    2) Changing the logic
      Remove validation
      Submit nonstandard data to the server
      Changing the state or events
      Directly invoking functionality within the component

    3) Recompile
      In most cases, you must recompile the adulterated source back into bytecode

      
      Java
        javac
          In the JDK
      Flash
        flasm
          reassemble the bytecode
          cant use this if what you modified was actionscript
        Flash development studios (from Adobe)
          if you modified the actionscript
      Silverlight
        Visual Studio

      Depending on the technology, you might need to re-archive the files (see above)
        For java/silverlight files
          repackage using the zip utility
            simply change the ext back to .jar/.xap

    4) Load back into browser OR (Load outside of browser- #5)
      Multiple ways
        browser cache
          replace the given file and restart the browser

          If you can't easily see the file, skip this method
        intercepting proxy
          Change the URL of the requested files
            This may be difficult because it can violate the browser's SOP
          
          Change the response body
            Often the easiest way

            If browser cached the executable, force it to refresh

            Find the response that contains the executable its message body
              Replace the body with your executable
                Can use Burp's "Paste from File" context menu option
      5) Executing outside the browser
          For these attacks, one needs to change the original executable into a standalone program
            Ex: Java
              main method
                Encapsulate the program in this method and you can run the program through the Java CLI

        Ex: An ext. validates the user input, obfuscates/encrypts the result, then submits a request to the server.
          In this situation, we'd simply be after the request (or, injecting into it).
            Strip out the validation and create a local version of the extension.
            
            Submit your input locally and get the obfuscated/encrypted output

            Make a request with the unadulterated extension (in the browser)

            Inject your local output into the request

Bytecode Obfuscation
  As it's very easy to decompile the bytecode, certain developers obfuscate the given source code.
    
    obfuscator program functionality.

      These programs can...

        Class, method, variables names
          Replaced with meaningless identifiers "a", "b", etc.

        replace item names with language specific keywords (ex: new)
          While this is technically 'illegal', most VMs will execute the code
            However, the compiler isn't so generous.  When one tries to recompile the source, there will be substantial problems.
              One most rename all of the illegally named items.
          Some decompilers have problems with his code
            Even if it CAN decompile, the source is VERY difficult to read
          












