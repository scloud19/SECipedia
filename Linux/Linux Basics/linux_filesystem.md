Linux File System
    Compression
        gzip
            aka GNU Zip
            will make a *.gz file

            Doesn't allow you to do an archive (aka pack multiple files/directories into one entity)

        gunzip
            to uncompress a .gz file

        Making a tarball
            tar cvf achive.tar file1 file2 directory1 ...
                allows you to archive files/directories

                tar switches (in general)
                    c - create mode
                    v - verbose diagnostic output
                    f - file option. The next argument on the CLI after the this switch must be the archive filename to create.  You must use this option at all times.
                    x - extract (unpack) mode
                    t - table of contents mode
                        Can peek inside tarball before extraction
                    p - preserve permissions while unpacking
                        This will override your umask and get the exact permissions specified in the archive

                To use stdin/stdout, enter a dash(-) in the place of the filename

        Unpacking the tar ball
            Before you unpack, it's a good idea to peek into the .tar file to see what's inside (i.e., invoke table-of-contents mode).
                tar tfvp archive.tar

                Given the file paths in the TOC, you can specify to only unpack certain files/directories in the tarball by entering their names as the last arguments to the command (above).

                Also, by looking at the TOC, you can make sure that the tarball isn't placing files in weird places.  
                    Make sure that the files are housed within the directory (not at random places all over)

            Ex: unpack tarball
                If needed, create a directory for the tarball and then extract (it can create a mess out of your PWD if extracted into it.)

            tar xvf archive.tar

                switches
                    x - extract (unpack) mode




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
