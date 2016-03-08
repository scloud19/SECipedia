Read through this entire doc before configuring



!! = Config that must be set
!? = An optional config

Before setting any configs, search this document and make sure there isn't any warnings, etc.

Create all of the following
  This will be the basis for the principle directory hierarchy
  Make sure that there's an apache group that only has the user apache
    httpd should do this by default

  /opt/modsecurity
    Main folder for modsec
    Per[mi]ssions
      Owner/Group
      root  apache  rwxr-x---

      Rationale
        Apache
          To get access to subfolders

  
  /opt/modsecurity/bin
    Binaries
    Permissions
      root  apache  rwxr-x---
        Rationale
          Apache may need to run some of these
  /opt/modsecurity/etc
    Configuration files
    Permissions
      root  root  rwx------

  /opt/modsecurity/var
    All of the items that are written to at runtime
    Permissions
      root  apache  rwxr-x---

      Rationale
        Apache
          To get access to subfolders


  /opt/modsecurity/var/audit
    Audit logs
    Permissions
      apache  root  rwx------

  /opt/modsecurity/var/data
    Persistent data
    Permissions
      apache  root  rwx------

  /opt/modsecurity/var/log
    Instead of its own logging directory, it may make sense to put this with all of the Apache logs
      Counterpoint: If utilizing the audit logging feature a lot, it may make sense to give it its own HD (to save on I/O operations for the main server)
      Still leverage the same permissions as shown below.
    Logs
    Permissions
      root  root  rwx------

      Rationale
        Folder contains log files that are opened by Apache early (while it's still root).
          Ex: if we gave the apache user access to this, they could put in malicious code that would be run by root.
          EX: OR utilize this as a basis for symlink attacks
          
          Ex: By submitting certain requests to Apache, one might be able to control exactly what is written to the log files and compromise the system.

  /opt/modsecurity/var/tmp
    Temporary files
    Permissions
      apache  apache  rwxr-x---

      Rationale
        Sometimes, you will have third party software scanning the items in tmp
          Ex: Scan uploaded files for viruses through ClamAV
          Any third party software should be added to the Apache group
  /opt/modsecurity/var/upload
    File uploads
      apache  root  rwx------

  Overall permission logic
    Root is owner of everything
      We only assign to Apache if necessary

Configuration File Layout
  modsecurity.conf
    The main entry point to modsecurity configs
    Reference this 1 file from Apache

    Contents:
      <IfModule mod_security2.c>
      # Main conf file
      Include /opt/modsecurity/etc/main.conf
      # Rules that run first
      Include /opt/modsecurity/etc/rules-first.conf
      # Main rule file
      Include /opt/modsecurity/etc/rules.conf
      Include /opt/modsecurity/etc/rules-last.conf
      </IfModule>

Adding ModSec to Apache
  httpd.conf
    Contents
      LoadFile /usr/lib/libxml2.so
      LoadFile /usr/lib/liblua5.1.so
      # Finally, load ModSecurity
      LoadModule security2_module modules/mod_security2.so

      Include /opt/modsecurity/etc/modsecurity.conf

Enabling ModSec
    ! "SecRuleEngine" directive
        Allows you to quickly turn on/off MS
        This directive always comes first
        Works inside of Apache's container tags
          Ex: <VirtualHost>, <Location>
          You can control exactly where MS runs          
        Recommended
          Start in detection only mode
            This ensures that nothing will be blocked
              # Attaching MS to every transaction
              ! SecRuleEngine DetectionOnly

Configs
  Info given in this format
    Ex: Directive name
          Directive Value
          # Comment

Request
  Consist of
    Headers
      Always present
    Body
      Optional

  # Allow MS to access request body
  # This will also buffer the req body in RAM
    # In most cases
  !! SecRequestBodyAccess On

  Directives that control how buffering is done
    Directives that control request limits
      These determine the max req body size that will be accepted for buffering
      !!SecRequestBodyNoFilesLimit 131072
        # Refers to to size of the data, with files excluded
        # You want this number as small as possible

      !!SecRequestBodyLimit 1310720
        # Max request body size we will accept for buffering
        # If you allow file uploads, you need to adjust for this number
        # Uploads generally dont use RAM
          # No opportunity for DoS, so its safe to allow
          # larger uploads

      !! SecRequestBodyInMemoryLimit
        Controls how much of a request body will be stored in RAM
          The rest will be streamed to disk
        This only works with file upload (multipart/form-data) requests
          "multipart/form-data"
            content type
            Should be used for submitting forms that contain files, non-ASCII data, and binary data
          NOTE: There are other content types that can send data, how do we mitigate against this? This only covers multipart/form-data


        !! SecRequestBodyInMemoryLimit 131072
          # Stores up to 128 KB of request body data in memory.  When the multipart parser reaches this limit, it will start using your HD for storage.


Request/Resp Buffer Size Configs
  # Should be corresponding configs for response
    (Replace Request with Response)
    Double check in docs
  !!SecRequestLimit
    ProcessPartial 
      # Stop accepting resp. body data, but it will NOT block

Response Body Handling
  !! SecResponseBodyAccess Off
  # Most deployments want to focus on incoming threats, so this is usually turned off.  This helps with memory consumption
  However, for zHost, you might want his on to protect the clients from faults WP configs, etc.

  Works best to detect:
    information leakage
    config errors
    traces of attacks
    verbose error messages that should be suppressed 


  If turned ON
    You usually only want to look at the bodies of some of the responses, not all
    Response bodies make the bulk of the traffic on most web sites
      The majority is just static files that dont really have security implications
        To assist, filter by MIME type on the response
          ! SecResponseBodyMimeType text/plain text/html null
          # Null = inspecting the response bodies for which the MIME type is unknown.
          # What types of files do you want to observe?
            # Ex: you wouldn't want to look at images, etc.

  !? SecResponseBodyLimit 524288
  # Buffers the response bodies up to this MAX

  !? SecResponseBodyLimitAction ProcessPartial
  # If a response body is larger than the previous config, we process what we have in the buffer and then let the rest through
  This is good because it doesn't BLOCK a website from giving more content than the buffer limit
  
  
  Dealing with Response Compression
    If you're leveraging MS in reverse proxy mode AND with backend servers that support compression
      set !! SecDisableBackendCompression On
        # To the backend servers, this will hide the fact that clients support compression
          This will then give MS access to uncompressed data
        If you DONT do this, MS will only see the compressed response bodies
        If you go this route, set mod_deflate in the proxy itself, so compression will occur in the reverse proxy
          The aforementioned MS directive won't interfere with its operation

File Upload Configs
  General
    File upload interception slows down MS and can potentially consume a lot of disk space
      Thus, only enable this functionality where you really need it

  # Location where MS will store intercepted uploaded files.  This location must be private to MS. We set this information even if we turn off the main functionality

  !! SecUploadDir /opt/modsecurity/var/upload

  # Default, do not intercept (nor store) uploaded files
  !! SecUploadKeepFiles Off

  # Uploaded files are by default created with permissions that do not allow any other user to access them.  If I utilize an antivirus, I'll need to change this.  Maybe create an antivirus group and give it permissions to this folder.  With the current permissions, only the apache user can access the files
    !! SecUploadFileMode 0600

  # Limit the number of files that MS will handle in one request
    !! SecUploadFileLimit 32

Debug Log Configs
  COST/BENEFIT
    Very useful for troubleshooting, but too much logging will affect performance
      In PROD, the recommended debug level is 3
      The debug messages go into Apache's Error Log, it its helpful to have all of the debug messages in one place
    
    # Debug log
    !!  SecDebugLog /opt/modsecurity/var/log/debug.log
    !!  SecDebugLogLevel 3

Audit Log
  The ability to record complete transaction data

  Size
    For a typical transaction without a request body this is about 1 KB
      Multiply this by the number of requests you're receiving daily
        This can add up quick, you want to keep this to a minimum

  # Log only RELEVANT transactions (warning reported against them)
  !! SecAuditEngine RelevantOnly

  # Log requests that cause a server error (response codes 500-599)
  !! SecAuditLogRelevantStatus ^5

  # By default, we log all transaction data except the response bodies
  !! SecAuditLogParts ABDEFHIJKZ

  # Use a single file to store all recorded information.
    This prevents remote logging and logging of lots of transactions
      I need to change this, as I'll be having failover across multiple regions for MS (thus hosted on different machines), I'll need remote logging functionality (a way of consolidating logs from different MS instances).  As there can also be a huge number of transactions, I'll need to a log rotation/archiving mechanism
        More info:
          https://github.com/SpiderLabs/ModSecurity/wiki/ModSecurity-Frequently-Asked-Questions-(FAQ)#how-do-i-manage-modsecurity-logs-if-i-have-multiple-installations

          https://github.com/SpiderLabs/ModSecurity/wiki/ModSecurity-Frequently-Asked-Questions-(FAQ)#is-there-an-open-source-console-to-send-my-audit-logs-to

          Look into concurrent logging (as referenced below)

    Thus, look into changing the args on the following directives
    !! SecAuditLogType Serial
    !! SecAuditLog /opt/modsecurity/var/log/audit.log

    # Configure the path for a more scalable audit logging scheme (concurrent logging)
    # With the given configs so far, concurrent logging isn't on (look into if this fits my needs for the problems listing above)

    ! SecAuditLogStorageDir /opt/modsecurity/var/audit

Rule Match policy
  What happens when a rule matches?

  Recommended that you start without blocking
    This mitigates false positives
    Monitor your traffic over time
  
  !! SecDefaultAction "phase:1,log,auditlog,pass"
  # This policy will work for all rules that follow it in the same configuration context
  # Don't block by default, this mitigates false positives
  # I need to watch the logs and deduce when to block

    It's possible to write rules that ignore the default policies
      Thus
        If using third-party rule sets
          And you aren't sure how they will behave, switch the entire engine to detection only by using SecRuleEngine
            In this context, no rules will ever block

Handling Processing Errors
  Types of processing errors
    Request/Response buffering limits reached
      in PROD, these happen a lot and shouldn't be a concern
    Parsing errors
      MS Parsers in General
        Raise flags when they fail, but also when they encounter something suspicious
      By checking the flags in your rules you detect processing errors

      # Verify that the request body has been correctly processed
      # In general, when MS fails to process a request body
        In blocking-mode
          the request should be rejected 
        In detection-only mode
          Log a high-severity alert
        This rule will be leveraged no matter which parser was used for parsing
      !! SecRule REQBODY_PROCESSOR_ERROR "!@eq 0" \
    "phase:2,t:none,log,block,msg:'Failed to parse request body: %{REQBODY_PROCESSOR_ERROR_MSG}'"

    multipart/form-data parser
      This is utilized to handle file uploads

      If there's an error from this parser, there's a good probability that an attacker is trying to bypass MS by manipulating the req body payload.



      # By default, be strict with what is accepted in the multipart/form-data request body.  If the rule is too strict, potentially change it to detection-only

      !! SecRule MULTIPART_STRICT_ERROR "!@eq 0" \
        "phase:2,t:none,log,block,msg:'Multipart request body \
        failed strict validation: \
        PE %{REQBODY_PROCESSOR_ERROR}, \
        BQ %{MULTIPART_BOUNDARY_QUOTED}, \
        BW %{MULTIPART_BOUNDARY_WHITESPACE}, \
        DB %{MULTIPART_DATA_BEFORE}, \
        DA %{MULTIPART_DATA_AFTER}, \
        HF %{MULTIPART_HEADER_FOLDING}, \
        LF %{MULTIPART_LF_LINE}, \
        SM %{MULTIPART_MISSING_SEMICOLON}, \
        IQ %{MULTIPART_INVALID_QUOTING}, \
        IF %{MULTIPART_INVALID_HEADER_FOLDING}, \
        FE %{MULTIPART_FILE_LIMIT_EXCEEDED}'"
  
  PCRE limit errors
    PCRE - Perl Compatible Regular Expressions

    Associated configs
      ! SecPcreMatchLimit
      ! SecPcreMatchLimitRecursion

    The lower the PCRE limits, the more difficult it is to subvert PCRE and the regular expressions to commit denial of service attacks.

    The debug log will ID rules that have exceeded the limits

    !! SecRule TX:MSC_PCRE_LIMITS_EXCEEDED "@eq 1" \
    "phase:5,t:none,log,pass,msg:'PCRE limits exceeded'"

Verify the install
  1) Add a blocking rule to detect something in a param.
    # Inspect all params for "dude, wheres my car".  If a match is found, respond with a 503
    ! SecRule ARGS "imahackeryo" "phase:2,log,deny,status:503"

  2) Restart Apache
  3) Send a GET request to the server
    Ex: server.com/?hello=imahackeryo
    A 503 should be given
  4) Verify that the Apache error log/debug log/audit log has all been updated
  5) Submit a POST that triggers the rule
    This tests if
      MS is inspecting the req body
      MS is properly feeding the body to the backend servers







