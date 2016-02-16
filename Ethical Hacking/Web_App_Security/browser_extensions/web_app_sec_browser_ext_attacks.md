There are two techniques that can be invoked when targeting applications that use browser exts.
  1) Intercept/modify the requests/responses to the server
    Easiest way to start testing the component

    Potential problems
      Data may be encrypted/obfuscated/serialized using schemes that are specific to the technology being used
        Thus, by only doing 1, you can't see the "core" of what is occurring

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

