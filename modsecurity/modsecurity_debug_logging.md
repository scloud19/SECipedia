[4] Audit log: Ignoring a non-relevant request.
  This means that ModSecurity didn't find anything of interest in the transaction, so it didn't log it.


Debug Log
  Primary troubleshooting tool
  Can be changed at runtime, on a per request basis.
    Done through the ctl action
      ctl
        Allows some of the configs to be updated at runtime
        See "ctl:debugLogLevel=9" below for ex
  vs Audit Log
    Audit logs can record all of the rules that matched a given transaction, however to figure out why some rules didn't match, you need the debug log

  Directives
    SecDebugLog PATH_TO_LOG_FILE
    SecDebugLogLevel DEBUG_LOG_LEVEL

  Levels
    0
      No logging
    1 
      Errors
      Ex:
        fatal processing errors
        blocked transactions
    2 
      Warnings
      Ex:
        non-blocking rule matches
    3 
      Notices
      Ex:
        non-fatal processing errors
    4
      Informational
    5
      Detailed
    9
      Everything!

  Deployment Considerations
    Keep the debug level at 3

  Ex Debug Log Line
  [18/Aug/2009:08:18:08 +0100] [192.168.3.111/sid#80f4e40][rid#81d0588][/index.html]
  [4] Initialising transaction (txid SopVsH8AAAEAAE8-NB4AAAAD).

    Moving from left-to-right (each an array)
      Time
      Client IP address/internal server ID
      Internal request ID
      Request URI
      Debug log level

    Non Array Portion
      The message
  Tips
    Debugging at a granular level

      Location directive based
        The data here still might be unmanageable

        Override the default config using the <Location> directive
          Send this output to its own file

        <Location /myapp/>
          SecDebugLogLevel 9
          SecDebugLog /opt/modsecurity/var/log/tslog.log
        </Location>

      Debugging on a per-request basis
        Via your IP
          Cons: Still can be too verbose

          Can do this in PROD (can change the debug log level at runtime)

          Ex: IDing your self by your IP and then observing the traffic
            SecRule REMOTE_ADDR "@streq 192.168.1.0" \
                phase:1,nolog,pass,ctl:debugLogLevel=9

        Via User Agent string
          Often the best choice.
            For every different type of request that you want to log, you can change your user agent string and set a rule in MS to log it
              Now, you can easily search your debug logs for a very granular situation

            Make your USER_AGENT_STRs similar enough for your rule to always detect them, but different enough to allow you to use a search function to quickly find the exact request.

            Ex: 
              SecRule REQUEST_HEADERS:User-Agent USER_AGENT_STR \
                  phase:1,nolog,pass,ctl:debugLogLevel=9

