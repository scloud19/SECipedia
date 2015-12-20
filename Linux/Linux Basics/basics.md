General Notes:
Remember that not all items listed in these notes are consistent across all linux distros.



Helpful commands
    File System
        lsblk - show hard drive structure and mountpoints

Composed of levels (in order)
    Hardware
        CPU, RAM, Disks, Network Ports

        Main memory (RAM) is perhaps the most important area of the hardware.
            In raw form, just a big storage area for bunch of 0s and 1s.
                bit
                    0/1
            This memory is where the running kernel and processes reside.

            All input and output from peripheral devices flows through main memory.

            The CPU
                Is just an operator on memory.

                It reads its instructions and data from the memory and write data back out to memory
        State
            Often heard when discussing memory, processes, the kernel, etc.

            A state is a particular arrangement of bits.
    Kernel
        See kernel.md


    User space
        Refers to the parts of main memory that the user processes can access.
            If a process makes a mistake and crashes, the consequences are limited and can be cleaned up by the kernel.
                Obviously, certain user processes (given the right permissions), can do serious harm
        user mode
            restricts access to a usually small subset of memory and safe CPU operations

        Is made of
            Processes aka user processes
                the running programs that the kernel manages
        Exs:
            GUIs, Servers, Shell


Linux File System
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

        Shell Shortcuts
            ~ - Home directory of respective user
                Ex: ~jason = /home/json
                    ~root = /root
                    ~ftp = /srv/ftp
                        Services often have their own accounts




