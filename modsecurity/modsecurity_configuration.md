Create all of the following
  /opt/modsecurity
    Main folder for modsec
    Permissions
      Owner/Group
      root  apache  rwxr-x---

      Apache's access is based
  
  /opt/modsecurity/bin
    Binaries
    Permissions
      root  apache  rwxr-x---

  /opt/modsecurity/etc
    Configuration files
    Permissions
      root  root  rwx------

  /opt/modsecurity/var
    All of the items that are written to at runtime
    Permissions
      root  apache  rwxr-x---

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