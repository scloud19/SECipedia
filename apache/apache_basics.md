# Run as root

Apache
  For tutorial
    Set UP Ec2 w/elastic IP and open up security group for port 80
  Install Apache on AWS Linux
    Put in bootup script
      You don't need to put sudo infront of these commands because the script is run as root

      yum install -y httpd
      service httpd start
        Starts the service immediately
      chkconfig httpd on
        Makes sure that the service will be started on reboot

    See if everything started up correctly internally
      netstat -ant | grep 80

    To make sure that our security group is working correctly
      Go to EC2 IP in URL

    If problems
      /var/log/httpd/error_log


  Apache Structure
    Modular Nature
      Modules are the "powerhouses" that run various parts of apache.  Directives are used to configure these modules and are place in /etc/httpd/conf/httpd.conf

      Server binary contains these modules
        core
        prefork
        http_core
        mod_so

        If you build from source, you can customize which of these modules are includes

      Although most of apache's features are loaded through "external" modules that are loaded dynamically
        /usr/lib64/httpd/modules

        LoadModule
          These modules can be loaded via the "LoadModule" directive in the configuration file

        If you go to the documentation https://httpd.apache.org/docs/
          httpd -v
            Find correct version for the docs

           If you find a given directive, you will see what module it maps to.
            Similarly, you can reference a module and see its associated directives.


  Tutorial: Segway to apache_config.md



