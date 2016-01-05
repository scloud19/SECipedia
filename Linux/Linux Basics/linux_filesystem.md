Linux File System

    inode
        A data structure used to represent a filesystem object (which can include a file/directory)

        Stores the attributes and disk blocks location(s) of the data
            The attributes can include: manipulation metadata (access, modify time, etc.) as well as owner and permission data.

        In general, a directory is a list of names that are assigned to inodes.  The directory contains an entry for itself, its parent, and each of its children.

    Proper to refer to "folders" as directories
    Like a tree
        / - Starts at "root"
        /bin - Binaries and other executable programs
        /etc - System configuration files
        /home - Home directories 
            Ex: /home/zachroof
                Personal files for a user (i.e. music files)
        /opt - Optional or third party software
            Ex: Google Earth
        /tmp - Temporary space, typically cleared on reboot
        /usr - User related programs, libraries, and docs
            Ex: /usr/bin
                Executables for a specific user

                /usr/lib
                /usr/sbin
                /usr/local
                    Locally installed software that is not part of the base operating system
                    ex: /usr/local/crashplan/bin
                    /usr/local/crashplan/etc
                    /usr/local/crashplan/lib
                    /usr/local/crashplan/log

                Sometimes there are alternative installation patterns
                    Ex: /etc/opt/myapp
                    /opt/myapp/bin
                    /opt/myapp/lib
                    /var/opt/myapp

                    Another can be
                        /usr/local/bin/myapp
                        /usr/local/etc/myapp.conf
                        /usr/local/lib/libmyspp.so
                    
                    Another
                        /opt/COMPANY_NAME
                        /opt/COMPANY_NAME/bin
                        /opt/COMPANY_NAME/etc

                    Another (company/product)
                        /opt/google
                        /opt/google/chrome
                        /opt/google/earth

                    Most of the time, applications that are not part of the base OS can bed installed in
                        /usr/local
                        /opt


        /var - Variable data
            Ex: Log files (/var/log)
        /cdrom - A mount point for cds
        /media - Some distros mount removable media here
        /mnt - Used to mount external file systems
        /proc - Provides info about running processes
        /root - Home directory for the root account 
        /sbin - System administration binaries
        /srv - Contains data which is served by the system
            Ex: /srv/www
                Web server files
        /sys - Used to display and sometimes configure the devices known to the kernel

        Shell Prompts
            $ - Normal User
            # - Root

        Symbolic Links
            A file that points to another file or directory

            In ls -la the first permission bit will be an "l"
                Think "link".

            Chained symbolic links
                sym links that point to other sym links

        Shell Shortcuts
            ~ - Home directory of respective user
                Ex: ~jason = /home/json
                    ~root = /root
                    ~ftp = /srv/ftp
                        Services often have their own accounts
