Apache
  Look in Apache folder

  Make sure to bake in modules for DoS evasion
    Ex: mod_evasion, etc.

  server monitoring
    Utilize mod_status
      There are security considerations, but you can leverage it in the firewall layer only (and be secure)
      apache_key_modules.md
      apache_troubleshooting.md

  Mod Security
    Deploy as a standalone reverse proxy
      See modsecurity_basics.md/reverse proxy

    See modsecurity_basics.md/Apache Security Considerations

AWS
  CloudWatch
    Make sure to set up triggers for abnormal spikes in CPU/memory
      If being DoSed in Apache, look at apache_troubleshooting.md


Linux
  Look into fstab and see if noatime is set.  If a partition is mounted with this option, there can be significant performance considerations.