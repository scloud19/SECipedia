See modsecurity_basics.md/apache security considerations

mod_status
  More info on mod_status
    apache_key_modules.md
  Security issues
    If mod_status is loaded into the server, its capability is available in ALL configuration files (this includes per-directory files, ex: .htaccess)
      This may give unwanted access to sensitive information