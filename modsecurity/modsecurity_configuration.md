Create all of the following
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

  /opt/modsecurity/var/upload
    File uploads
      apache  root  rwx------

  Overall permission logic
    Root is owner of everything
      We only assign to Apache if necessary