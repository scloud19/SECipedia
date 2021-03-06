Look over my WAHH notes and make sure that the site is secure.

Search for @@ in all zsheets notes. these are important items to consider in deployment or for this project.

Make sure to read core_defenses.md

Before doing my own configs, search the internet for secure Configuration guides on a given piece of software
  Ex: https://www.nsa.gov/ia/mitigation_guidance/security_configuration_guides/index.shtml


Overall
  cryptography_attack_mitigation.md

In all main folders that will be leveraged for the deployment
  Read the _good_resources.md

Review everything in this document before deployment

Everything you utilize, make sure to get notifications of updates.

  Look at https://github.com/SpiderLabs/ModSecurity/wiki/Reference-Manual#Installation_for_Apache

  Make sure that the Apache version is the latest
    Aka you might need to install from source
    
  Look in Apache folder


  Make sure to bake in modules for DoS evasion
    Ex: mod_evasion, etc.

  server monitoring
    Utilize mod_status
      There are security considerations, but you can leverage it in the firewall layer only (and be secure)
      apache_key_modules.md
      apache_troubleshooting.md

  Look at modsecurity_good_resources.md
  modsecurity_installation.md
  modsecurity_configuration.md


    Deploy as a standalone reverse proxy
      See modsecurity_basics.md/reverse proxy

    See modsecurity_basics.md/Apache Security Considerations

    modsecurity_performance_considerations.md

    Look at leveraging third party rulesets as a base
      Do they have ones for WordPres?
      Exs: Core Rule Set project (OWASP)

AWS
  CloudWatch
    Make sure to set up triggers for abnormal spikes in CPU/memory
      If being DoSed in Apache, look at apache_troubleshooting.md

  Firewall Ports
    2083 - Cpanel SSL access
    2082 - Cpanel nonSSL access

  By default, some distros (including Amazon Linux) create ext2 FS's by default.  You want ext4
    

Linux
  Linux hardening
    https://github.com/lfit/itpol/blob/master/linux-workstation-security.md
    Search Linux Security on PLuralsight
  Cloud Linux
    Install before installing anything else
  Look into fstab and see if noatime is set.  If a partition is mounted with this option, there can be significant performance considerations.

  SSL
    https://www.ssllabs.com/projects/best-practices/index.html

    Accuracy
    The strength of cryptography can't be measured accurately
      Due to this, you'll find many different, albeit similar, recommendations
    ENISA 
      European Union Agency for Network and Information Security

      Provides guidance on what types of key lengths to use for certain use cases
        To view ENISA and other recommendations
          https://www.keylength.com/
            Out of all of them, try to model off of ENISA

    Additional Info
      https://www.feistyduck.com/library/bulletproof/online/ch-ssl-tls-crypto.html
        Table 1.3
          An encryption strength mapping for common key sizes


Cpanel/WHM
  go through cpanel_install_setup.md
  Should I allow dedicated IPs for servers? Or, just share 1 static IP amongst all clients?

  Always monitor
    /usr/local/cpanel/logs/*
  
  Any way to force bcrypt for this?
    https://documentation.cpanel.net/display/ALD/Tweak+Settings+-+Security#TweakSettings-Security-UseMD5passwordswithApache


