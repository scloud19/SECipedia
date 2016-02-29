 Linux File System
    Partitioning vs File System manipulation
        Remember, there is a big difference.
        Partitioning
            The boundaries of the disk
        File System
            A much more involved data system.
    
    Filesystem capacity
        df
            Shows the size and utilization of the currently mounted FSs
            -m
                List capacities in 1MB blocks
            -h
                Best guess at what a person can read (Maybe GB)
        du
            Prints the disk usage of every directory in the directory hierarchy (starting with the CWD)

            -s 
                Summary mode
                To summarize the disk usage of all of directories in the CWD
                    du -s *


    Filesystem
        What you see when you run ls, cd, etc.
        A form of database
        Provides the structure to transform a simple block device into a hierarchy of files

        SCSI subsystem
            Standardizes the communication between different device types and kernel control commands

        Dont just contain just normal "files"
            See 'Special FSs' below
            /sys and /proc
                System interfaces

        Where traditionally implemented in the kernel
            File System in User Space (FUSE)
                A more recent revelation that allows user-space file systems.

        Virtual File System (VFS)
            An abstraction layer that allows Linux to support a huge number of file systems.

            Makes sure that all FS implementations support a standard interface so that user-space apps can access files and directories in the same manner.

        Common Filesystem Types
            (the filesystem in Linux is in the parens)

            Extended filesystem (ext*)
                (ext4)
                    Current iteration of a line of filesystems native to Linux

                Backwards compatibility in ext series
                    If (ext2), (ext3), can mount:
                        As each other
                        As (ex4)
                    If (ex4), can't mount:
                        As (ext2), (ext3)
            ISO 9660 (iso9660)
                FS used in CD-Roms

            FAT filesystems (msdos, vfat, umsdos)
                Microsoft systems

                msdos
                    Supports the very old monocase variety of MS-DOS systems

                vfat
                    Used in most modern windows filesystems
                    Use this to get full access from Linux
                HFS+ (hfplus)
                    Mac systems










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
            Changing the partition tables
                Makes it difficult to recover any data on partitions that you delete
                    This is because it changes the initial point of reference for a file system
                    Thus, backup if you're partitioning a device with critical data

                Make sure that no partitions on your target disk are currently in use
                    This is a concern because most distros mount any detected FS

            A small portion of the disk where the partitions are defined.



            There are many kinds of partition tables.
                Master Boot Record (MBR)
                    Houses the traditional MBR

                Globally Unique Identifier Partition Table (GPT)
                    A newer standard

            Linux partitioning tools
                parted
                    View more about parted below in this file
                    Text based tool that supports both MBR and GPT

                    While this does have the functionality to create and resize filesystems: it shouldn't be used for file system manipulation because it can be confusing (as there are large differences between the two).

                gparted
                    A GUI for parted
                fdisk
                    The "standard"
                    Doesn't support GPT
                gdisk
                    A version of fdisk that supports GPT but not MBR


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
        /lost+found - Ex: fsck can place files here if they have lost a name
        /mnt - Used to mount external file systems
        /proc - Provides system statistics through a       browsable directory-and-file interface.
             Provides info about running processes and kernel parameters
             There's a movement to place non-process information out of /proc/ and into /sys
        /root - Home directory for the root account 
        /sbin - System administration binaries.  Regular users usually do not have /sbin in their PATH.  For many of this items, you need to be running as root.  Remember: System Binaries = /sbin
        /srv - Contains data which is served by the system
            Ex: /srv/www
                Web server files
        /sys - Used to display and sometimes configure the devices known to the kernel.  This is very similar in nature to /proc  It also provides a system interface.
            This can also show more "low level" items
                Ex: bus, firmware, power
            Also see /sys notes in linux_devices.md

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

Partitioning Tools/processes of Note
    To view partition changes
        udevadm monitor --kernel
    To see partitions in general
        /proc/partitions
        /sys/block/device
            altered partition system interfaces
        /dev
            For altered partition devices

    parted
        When partitioning, be careful.  As you issue the commands, parted makes changes in real time.  You don't get a chance to review the changes.

        Modifies the partitions entirely in user space
            There is no need to provide kernel support for rewriting a partition table because the user space can read and modify all of a block device.

            Although, at the end, it does signal the kernel when individual partitions are altered, so the kernel can keep track.
        -l
            View the systems partition table
            Output

                partition table: msdos
                    Traditional MBR partition table
                    Names don't exist under this scheme
                        primary partition
                            Normal subdivision of a disk
                            There are a limit of 4 primary partitions
                        extended partition
                            If you want more than 4 partitions (you exhausted all of the primary partitions), you can designate one partition as a extended partition
                        logical partitions
                            Are used to extend the "extended partition"
                            These can be used like any other partitions in the operating system


Misc Notes on File Systems/Disks
    SSDs
        Are radically different from spinning disks in terms of their access characteristics.
            Ex: For SSDs, random access is not a problem because there's no head to sweep across the platter (aka the "record" looking part)

        Data reading process
            Data is read in chunks (typically 4096 bytes at a time)
                So, the read must begin at a multiple of that size



        Factors effecting performance
            Partition alignment
                If your partition (and its data) do not lie on a 4096 boundary
                    You may have to do two reads instead of one for common operations
                        Ex: reading contents of a directory

                parted includes functionality to put newly created partitions at the proper offsets

    Buffering
        Write Buffering
            Linux buffers writes to the disk
                Means that the kernel usually doesn't immediately write changes to FSs when a process requests a change.
                    Instead, changes are stored in RAM until the kernel can conveniently persist the changes to disk.
            # umount
                The kernel automatically flushes all changes to disk
            # sync
                Manually flushes the cache to disk

        Read Buffering
            The kernel has functionality that utilizes RAM to automatically cache blocks read from disk.
                If a process has to repeatedly access a file, the kernel doesn't have to go to the disk over and over.
                    It reads from the RAM cache and saves time.

Checking and Repairing Filesystems
    Filesystems are basically a sophisticated database mechanism

    fsck MOUNT_POINT || FSTAB_NAME
        Caveat
            ext3, ext4 FSs
                You normally dont have to check these systems manually with fsck because the journal system ensures data integrity
                    Although, these FSs can still break
        WARNING
            Never use this on a mounted FS
                The kernel may alter the disk as you run the check, which can cause runtime mismatches.
                    EXCEPTION: Mount / read-only in single-user mode
            fsck can seriously mess up a FS that is plagued by larger problems
                Ex: hardware failure, device misconfiguration
                You can usually see that there's a larger problem if fsck asks a LOT of questions in manual mode
                    If you think that there might be other problems
                        fsck -n
                            To check the FS without modifying anything
                            Then run fsck for real


        Tool utilized to check a filesystem
        Recovering the superblock
            Ex: if someone wrote to the beginning of the disk partition
            mkfs superblock backup
                You might be able to recover the FS with one of the superblock backups that mkfs creates
            Solution
                1) If you don't know the superblock
                    mkfs -n
                        Potentially view a list of superblock backup numbers (without adulterating data)
                2)    fsck -b NUM
                    Replace the corrupted superblock with one at NUM
                
        -p
            Automatically fixes ordinary problems without prompting the user a million times
            aka: -a
            many linux distros use some form of -p at boot
        If fsck finds a problem, it will prompt you for a next step
            Example prompts
                Reconnecting an inode
                    The prog has found a file that doesn't appear to have a name
                        When reconnecting this file, the fsck will place the file in "lost+found" of the FS, with a number as the filename.

                        You'll need to guess the name based on the content of the file
Special FSs
    Not all filesystems represent storage on physical media
        Some are essentially system interfaces
            Ex: show system information such as process IDs, diagnostic information,etc.
    proc (aka process)
        Found in /proc
        Note: There's currently a push to move information unrelated to processes into /sys
            So unprocess related info in /proc may be depreciated
        Each numerical directory in /proc is a current PID on the system.
            The files in these PID directories contain metadata about the PID
        /proc/self
            The current process
        /proc/cpuinfo
            Additional kernel and hardware information
    sysfs
        /sys
            See notes above
    tmpfs
        CURRENTLY AT

Severe Disk Problems
    Solutions
        debugfs
            (Start with this solution first)
            Allows you to look through the files and copy them elsewhere.
            Will open the FS in read-only mode

        dd
            Try to extract the entire FS image from the disk with dd and transfer it to a partition on another disk of the same size

            You still need to repair the FS before you mount it
        Patching and Salvage
            Try to patch the FS as much as you can, then mount it in read-only mode

            You still need to repair the FS before you mount it




