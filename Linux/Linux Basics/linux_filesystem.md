 Linux File System
    File system layout
        Look at disk_structure.md

        Partitions
            Subdivisions of the whole disk

            The kernel presents each partition as a block device 


            /dev/sda
                Whole block device

            /dev/sda1
                Partition of the device

            From a sysadmin perspective, it can be prudent to make a partition just so users don't take up too much space and crash the system

        Partition Table
            A small portion of the disk where the partitions are defined.

        The next layer after the partition is the filesystem

    Filesystem
        The database of files and directories that you interact with in the user space

        When you try to access the data in a file
            Also see disk_access_process picture

            The partition table is leveraged to get the appropriate partition location
            Then,  the filesystem database on that partition is searched for the desired file data

        Ways to access a file
            See disk_access_process
            
            Through the..
                File system

                Directly through the disk device






    Compression
        bzip2
            *.bz2 files

            Compacts files a little more than gzip (but it is a little slower)

        bunzip2
            Decompress .bz2 files

            switches for bzip2 utilities (compressing/decompressing) are close enough to gzip/gunzip that you don't need to learn anything new.

        xz
            decompression CLI: unxz

            arguments are similar to those of gzip.

        gzip
            aka GNU Zip
            will make a *.gz file

            Doesn't allow you to do an archive (aka pack multiple files/directories into one entity)

        gunzip
            to uncompress a .gz/.Z file

            switches
                -d
                    decompresses
                -c
                    sends the result to STDOUT

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
            file.tar.gz
                is the same format as file.tgz

                This file is an archive (and it's compressed).  When working with files of this nature, move from right to left.  Thus, you must utilize gunzip before you can unpack the archive

                Ex: gunzip file.tar.gz
                    Then proceed with the unpacking instructions below

                    Although this is a little early, you can do both steps via
                        zcat file.tar.gz | tar xvf -

                        zcat is the same as gunzip -dc

                    To be even more efficient, you can do:
                        tar zxvf file.tar.gz

                            -z
                                automatically invoke gzip/gunzip on the archive
                                    this can be utilized for both extracting an archive (-x OR -t) OR creating one (-c)

                            -j
                                Same exact idea as -z, but for bzip2/bunzip2

                            This shows the table of contents for the tarball





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
        /dev - Contains device files.
        /bin - Binaries and other executable programs.
            Most of the basic unix commands.  Most of the programs in /bin are in binary format, having been created by a C compiler.  But some are shell scripts in modern systems.
        /etc - System configuration files.
            Contains the user password, boot, device, networking, and other setup files.
            Ex: /etc/X11
                Contains graphics card and window system configs.
        /home - Home directories 
            Ex: /home/zachroof
                Personal files for a user (i.e. music files)
        /opt - Optional or third party software
            Ex: Google Earth
            Many systems dont use /opt
        /lib - An abbreviation for library.  Contains library files containing code that executables can use.
            2 types of libraries: static/shared
                /lib should only contain shared libraries, but other lib directories (i.e. /usr/lib) contain both varieties as well as other auxiliary files. 
        /tmp - Temporary space, typically cleared on reboot.  Any user may read to/write from /tmp, but the user may not have permission to access another user's files there.  Many binaries use this directory as a workspace.

        /usr - User related programs, libraries, and docs.  This includes the bulk of the Linux system.  Many of the directory names in /usr are the same as those in the root directory (see directly below) and they hold the same type of files.  Most of the user-space programs and data reside.

            Ex: /usr/bin
                Executables for a specific user

                /usr/share/
                    Contains files that should work on other kinds of unix machines.
                        In the past, networks of machines would share this dir, but this is becoming rare with advances in disk storage on computers.  Also, maintaining this directory can be very difficult.

                        You can find in share: /man,/info 
                /usr/man
                    Contains manual pages
                /usr/lib
                /usr/sbin
                /usr/local
                    Locally installed software that is not part of the base operating system
                        Its structure should follow / and /usr
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
                    
                    /usr/include
                        Contains header files used by the C compiler
                    /usr/info
                        GNU info manuals



        /var - Variable data, where programs record runtime information.  This includes system logging, user tracking, caches, and other files that system programs create and manage are here.
            Ex: Log files (/var/log)

            There's also a /var/tmp directory, but the system doesn't wipe it on boot.
        /cdrom - A mount point for cds
        /boot - Contains kernel boot loader files.  These files pertain only to the first stage of the linux startup procedure.  This directory doesn't include information pertaining to service startups.

        /media - Some distros mount removable media here.  This can be a base attachment point for removable media such as flash drives. 
        /mnt - Used to mount external file systems
        /proc - Provides system statistics through a       browsable directory-and-file interface.
             Provides info about running processes and kernel parameters
        /root - Home directory for the root account 
        /sbin - System administration binaries.  Regular users usually do not have /sbin in their PATH.  For many of this items, you need to be running as root.  Remember: System Binaries = /sbin
        /srv - Contains data which is served by the system
            Ex: /srv/www
                Web server files
        /sys - Used to display and sometimes configure the devices known to the kernel.  This is very similar in nature to /proc  It also provides a system interface.

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
