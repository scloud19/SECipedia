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


