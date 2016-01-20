lilo (LInux LOader)
  A lot of Linux distros use this for booting operating systems.

  However, GRUB is much easier to use and more flexible.

  Both GRUB and lilo support dual boot installations


Overall boot process
  1) An x86 computer is booted, the processor looks at the end of the system memory for the BIOS (Basic Input/Output System) and runs it.
    BIOS
      Provides the lowest level interface to peripheral devices and controls the first step of the boot process.

  2) BIOS tests the system, looks for and checks peripherals, and then looks for a drive to use to boot the system.
    Usually it starts at the CD-ROM drive for bootable media first, then progresses to the HD.
      This load order is usually housed within a BIOS configuration

  3) If no bootable media is found on the CD-ROM drive, etc. The BIOS looks for a Master Boot Record (MBR) starting at the first sector on the first hard drive.  If this is found, the MBR is loaded into memory, and then the BIOS passes control to it.
    Sector
      A subdivision of a track on a magnetic or optical disk.
    MBR

