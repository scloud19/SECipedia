!! = Config that must be set

Create all of the following
  This will be the basis for the principle directory hierarchy
  Make sure that there's an apache group that only has the user apache
    httpd should do this by default

  /opt/modsecurity
    Main folder for modsec
    Permissions
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
      !!SecRequestNoFilesLimit 131072
        # If uploads are supported, this is the largest file
        # size that you will accept
        # Uploads generally dont use RAM
          # No opportunity for DoS, so its safe to allow
          # larger uploads
      !!SecRequestBodyLimit 1310720
        # Max size of the request (for non-file uploads)
        # Make this as small as possible
        # Research this number for use case

Request/Resp Buffer Size Configs
  # Should be corresponding configs for response
    (Replace Request with Response)
    Double check in docs
  !!SecRequestLimit

  !!SecRequestBodyLimitAction
    ProcessPartial 
      # Stop accepting resp. body data, but it will NOT block


  