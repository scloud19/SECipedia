See modsecurity_basics.md/apache security considerations

mod_status
  More info on mod_status
    apache_key_modules.md
  Security issues
    If mod_status is loaded into the server, its capability is available in ALL configuration files (this includes per-directory files, ex: .htaccess)
      This may give unwanted access to sensitive information


Apache log file directory
    Logs
    Permissions
      root  root  rwx------

      Rationale
        Folder contains log files that are opened by Apache early (while it's still root). (Double check this)
          Ex: if we gave the apache user access to this, they could put in malicious code that would be run by root.
          EX: OR utilize this as a basis for symlink attacks
          
          Ex: By submitting certain requests to Apache, one might be able to control exactly what is written to the log files and compromise the system.