lilo (LInux LOader)
  A lot of Linux distros use this for booting operating systems.

  However, GRUB is much easier to use and more flexible.

  Both GRUB and lilo support dual boot installations

GRUB
  Direct loading
    The boot method used by GRUB.  Its name comes from the fact that instructions are used to directly load the operating system.  (I.e. There's no "middle man" code between the boot-loader and the kernel.)
      CURRENTLY_AT (GRUB FEATURES: http://www.tldp.org/LDP/intro-linux/html/sect_04_02.html)
      

Overall boot process
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
  4) The MBR loads the boot-loader, which takes over the process (assuming the boot-loader is installed in the MBR)
    Aside:
      In the default Red Hat configuration, GRUB uses the settings in the MBR to display boot options in a menu.

  5) Once GRUB has received the correct instructions to boot the operating system, either from its command line or config file, it finds the necessary boot file and hands off control to the OS.



