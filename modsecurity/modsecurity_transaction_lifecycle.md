In MS, every transaction goes through 5 phases
  In each phase, MS will do some work
    In the beginning
      Ex: Parse data that has become available
    Middle
      Invoke rules speified to work in the given phae
    After
      A few "clean up" activities once the phase has finished
  
  Phase 1: Request headers
    First entry point for MS.
    
    Allows rule writers to assess a request before the request body processing is undertaken (which is relatively CPU intensive).

    Allows you to influence how MS will process the req body (Phase 2)
      Ex: In Phase 2, MS wont parse an XML request body by default 
        Although, if you place the appropriate rules into Phase 1, then this can happen.

  Phase 2: Request Body
    Main request analysis phase
    
    Takes place immediately after a complete request body has been received and processed

    Rules in this phase can leverage all of the request data

  Phase 3: Response Headers
    Takes place after the response headers become available, but before a response body is read.

    Rules that need to decide whether to inspect a response body should be placed here.

  Phase 4: Response Body
    Response analysis

    By the time this phase starts, the response body will have been read
      All of the response body data can be leveraged by the rules

  Phase 5: Logging
    The only phase which you can't block

    At this point in the lifecycle, the transaction has finished

    Rules in this phase are run to control how the logging is done.


    CURRENTLY AT
      https://www.feistyduck.com/library/modsecurity-handbook/online/ch01-introduction.html

      "LIFECYLE EXAMPLE"

