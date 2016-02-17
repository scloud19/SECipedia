There are two techniques that can be invoked when targeting applications that use browser exts.
  1) Intercept/modify the requests/responses to the server
    Easiest way to start testing the component

    Review responses from the server that trigger key client-side logic
      Many times, a timely modification of a server response may "unlock" the client GUI
        This can make to easy to perform certain privileged actions

      When certain critical events occur in the app (aka rolling the dice or drawing a card in a casino ext), does the app make any communication with the server?
        If not, there's an exploit in the client that can be leveraged.

    Potential problems
      Data may be encrypted/obfuscated/serialized using schemes that are specific to the technology being used
        Thus, by only doing 1, you can't see the "core" of what is occurring

        If the ext is using a proprietary encoding/encryption mechanism (that doesn't have an way to "unpack"), you need to decompile/debug the ext being used.


  2) Target the component itself
    attempt to decompile its bytecode to view the original source

    interact dynamically with the component using a debugger
      
      Pros:
        If done in a thorough manner, you can id all functionality that the component supports/references

        Allows you to modify key data submitted in requests (regardless of encryption/obfuscation)

      Cons:
        Time-consuming

  Try to use a combination of 1 and 2.


  1) Intercepting traffic
    If the browser is already set up to leverage an intercepting proxy, then you might not need any additional setup.
      Try this first, if the data is obfuscated look at the serialization.md notes



