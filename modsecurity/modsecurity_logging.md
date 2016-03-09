[4] Audit log: Ignoring a non-relevant request.
  This means that ModSecurity didn't find anything of interest in the transaction, so it didn't log it.


Debug Log
  Primary troubleshooting tool

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
      Location Based
        The data here still might be unmanageable

      Override the default config using the <Location> directive
        Send this output to its own file

      <Location /myapp/>
        SecDebugLogLevel 9
        SecDebugLog /opt/modsecurity/var/log/tslog.log
      </Location>

      Debugging on a per-request basis
        Ideal for debugging because you can do this in PROD (can change the debug log level at runtime)

