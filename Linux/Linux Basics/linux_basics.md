Shortcuts
    CLI shortcuts are in the linux shortcuts DIR.

General Notes:
Remember that not all items listed in these notes are consistent across all linux distros.

Display Modes (CURRENTLY AT)
    Two primary modes (Text mode and X Window System Server)
        X Window System Server
            GUI
    

Difference between hard links and sym links

    Sym links
        point to another file by name.  In other words, its contents is a path to a file.
            This path doesn't have to exist, it's just a pointer to a path.

    Hard links
        A hardlink isn't a pointer to a file, it's a directory entry (a file) pointing to the same inode. Even if you change the name of the other file, a hardlink still points to the file. If you replace the other file with a new version (by copying it), a hardlink will not point to the new file. You can only have hardlinks within the same filesystem. With hardlinks you don't have concept of the original files and links, all are equal (think of it as a reference to an object). It's a very low level concept.



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
    Ex: /dev/tty*, /dev/pts/*, /dev/tty
    A device for moving characters between a user process and an I/O device, usually for text output to a terminal screen.

    pseudoterminal devices
        Emulated terminals that understand the I/O features of real terminals, but instead of talking to a real piece of hardware, the kernel persents the I/O interface to a piece of software.
            Ex: shell terminal




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








