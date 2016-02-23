Boot options
  Single user mode
    Mode in which a multiuser computer boots into a single superuser

lilo (LInux LOader)
  A lot of Linux distros use this for booting operating systems.

  However, GRUB is much easier to use and more flexible.

  Both GRUB and lilo support dual boot installations

GRUB
  Supports both Direct Loading and Chain Loading

  Direct loading boot method
    The boot method used by GRUB.  Its name comes from the fact that instructions are used to directly load the operating system.  (I.e. There's no "middle man" code between the boot-loader and the kernel.)
      CURRENTLY_AT (GRUB FEATURES: http://www.tldp.org/LDP/intro-linux/html/sect_04_02.html)
  
  Chain Loading Boot Method
    Occurs in various proprietary OS's

    In operating system boot manager programs, chain loading is used to pass control from the boot manager to a boot sector. The target boot sector is loaded in from disk, replacing the in-memory boot sector from which the boot manager itself was bootstrapped, and executed.

    The MBR points to the first sector of the partition holding the operating system.  At this point, it finds all of the files necessary to boot the OS.
      

Overall boot process
  Also see: https://en.wikipedia.org/wiki/Booting#BOOT-LOADER
  1) An x86 computer is booted, the processor looks at the end of the system memory for the BIOS (Basic Input/Output System) and runs it.
    BIOS
      Provides the lowest level interface to peripheral devices and controls the first step of the boot process.

  2) BIOS tests the system, looks for and checks peripherals, and then looks for a drive to use to boot the system.
    Usually it starts at the CD-ROM drive for bootable media first, then progresses to the HD.
      This load order is usually housed within a BIOS configuration

  3) If no bootable media is found on the CD-ROM drive, etc. The BIOS looks for a Master Boot Record (MBR) starting at the first sector on the first hard drive.  If this is found, the MBR is loaded into memory, and then the BIOS passes control to it.
    Sector
      See hardware/disk.md
    MBR
      Contains instructions on how to load the GRUB (or LILO) boot-loader, using a pre-selected OS.

      In some installation processes (Ex: Windows and DOS), the MBR is completely overwritten without incorporating any of the current MBR's configuration.  So, if you previously installed Linux; that portion of the MBR will be overridden.


  4) The MBR loads the boot-loader, which takes over the process (assuming the boot-loader is installed in the MBR)
    Aside:
      In the default Red Hat configuration, GRUB uses the settings in the MBR to display boot options in a menu.

  5) Once GRUB has received the correct instructions to boot the operating system, either from its command line or config file, it finds the necessary boot file and hands off control to the OS.

  6) Once the kernel is loaded, it executes /sbin/init
    Init
      When init starts, it becomes the parent of all processes that automatically start when you boot your system.

      /etc/inittab
        The first file that init looks at

        Describes how the system should be set up in each run level and sets a default run level
          Run Level
            A configuration of processes

            All *nix systems systems can be run in different process configurations, such as single user mode.

            Ex: Run Level 1 (aka Single User Mode, Run Level S)
              Only the sysadmin can connect to the system

              Used to perform maintenance tasks without the risks of damaging the system or user data.

              All user services will be disabled.

            Ex: Reboot Run Level (Run Level 6)
              Shuts down all running services and restarts the system.

CURRENTLY AT (http://www.tldp.org/LDP/intro-linux/html/sect_04_02.html)  After having determined the default run level for your system