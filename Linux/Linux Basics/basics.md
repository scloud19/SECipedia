Shortcuts
    CLI shortcuts are in the linux shortcuts DIR.

General Notes:
Remember that not all items listed in these notes are consistent across all linux distros.




Help
    man
        -k
            Search man pages by keyword.
            Helps if you don't really know the name of the command that you want

            Ex: man -k tcp
                OUTPUT: tcp(4) Internet Transmission Protocol

                Output contains the manual page name, the manual section, and a quick description

        Manual Sections
            1- User commands
            2- System calls
            3- Higher-level Unix programming library docs
            4- Device interface and driver information
            5- File descriptions (system config. files)
            6- Games
            7- File formats, conventions, and encodings
                Ex: ASCII, suffices, etc.
            8- System commands and servers

            man 5 passwd
                Retrieves passwd information relative to section 5 

        Man always shows the first page when matching a search term
        Ex: man passwd
            Will list passwd(1)

            If you want section 5 info
                man 5 passwd

    COMMAND -h or --help

    info COMMAND
        More detailed man pages

    /usr/share/doc
        Some packages don't use man/info and put their help docs in this directory






Command Path (aka "path")
    A list of system directories that the shell searches when trying to run a command (ex: ls)

    Ex: If multiple "ls" binaries are found
        Shell runs the first matching program

    PATH
        Environment variable that contains the command path

        The different parts of the command path are delimited by a colon

        Ex: In your current shell session, add a directory to the beginning of the path
            PATH=dir:$PATH








shell variables
    A temporary variable that is stored in the shell.

    Can control the way that the shell behaves
        Ex: The bash shell reads the PS1 variable before displaying the prompt

    Variable assignment in the shell
        AM_I_DA_MAN=Yes!
            To access the variable you need to prepend '$'

    environment variables (ENV variable)
        vs. shell variables
            environment variables are like a shell variable, but it's not specific to the shell

            The main difference between the two is that the OS passes all of your shell's environment variables to programs that the shell runs, whereas shell variables can't be access in the commands that you run.

        To assign an ENV variable utilize export
            Ex: Create a shell variable and also make it an ENV variable
                AM_I_DA_MAN=YES!
                export AM_I_DA_MAN

        All processes on Unix systems have environment variable storage

        Many CLI programs make use of env variables.  For ex: you can feed less's flags into an ENV variable so you don't have to specify it every time.
            In the man pages, you will see a section named ENVIRONMENT which delineates these types of situations.


dot files
    config files that start with "."

Absolute paths
    Start with "/"
        Ex: /var/www/
        Any path that doesn't start with "/" (as seen above) is a relative path

Globbing
    The shells ability to match simple patterns to file and directory names

    Wont match dot files unless you explicitly enter ".*"

    Glob characters (there are more than just *,?)
        *

        ?
            Gives a "wildcard" for only 1 character
            Ex: zac?
                Matches zach, zack (and more...)

            Ex: Find all three letter files/directories in the current directory.
                echo ???

    The shell matches arguments containing globs to filenames, then will substitute the filenames, and finally will run the new command (with appropriate args)
        The substitution process is called expansion.
            If you don't want expansion to be performed on a glob, just surround it in single quotes
                Ex: echo '*'

        Important takeaway: The shell performs expansions before running the command.

    Ex: *securingthestack*
        Expands all file names that contain securingthestack inside the name.




The Bourne Shell
    /bin/sh

    The Bourne shell was developed at Bell Labs for early versions of Unix.  Every Unix system needs the Bourne shell in order to function correctly.

terminal
    Simply a window with access to the shell

bash
    /bin/sh

    "Bourne-again" shell

    An enhanced version of the Bourne shell that is used in Linux.

    Is the default shell on most Linux distros

shell
    A program that runs commands
        Ex: the commands that you enter

    chsh
        command to change shells

    Also serves as a small programming environment

    shell scripts
        text files that contain a sequence of unique commands

    There are many different types of shells, however most inherit their features from the Bourne shell

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


    User space aka "userland"

        Refers to the kernel allocated parts of main memory that the user processes can access.

            If a process makes a mistake and crashes, the consequences are limited and can be cleaned up by the kernel.
                Obviously, certain user processes (given the right permissions), can do serious harm
        user mode
            restricts access to a usually small subset of memory and safe CPU operations

        Is made of
            Processes aka user processes
                the running programs that the kernel manages

                user processes make up the environment that you directly interact with. 
        Exs:
            GUIs, Servers, Shell

        In general, a process is simply a state (or image) in memory.
            As such "user space" can refer to the memory of the entire collection of running processes.

        Most of the "real action" occurs in the user space
            Refer to user_processes.png
                From the kernel's POV, these processes are the same.

                Shows the layered structure of the types of system components that user processes represent.
                    Network Configuration, etc.
                        Is the closest to the kernel and performs a more singular (simple) task, relative to the layers above.

                    Each layer builds on eachother, you can see the emphasis on modularity. 
                        If a module wants to use another, it's usually at a lower layer.

                    Top layer is the closest to the user's control

        Every user-space process has a user owner
            User in *nix
                An entity that can run processes and own files

                Has a username "zroof" but the kernel manages by numeric ids called "userids"

            Processes are said to run as the owner.

            Root user
                Still runs in the OS's user mode, NOT kernel mode




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




