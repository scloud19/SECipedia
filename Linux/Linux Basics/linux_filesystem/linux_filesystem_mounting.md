FS = File system

mounting
  The process of attaching a file system

  Common terminology
    "mount a DEVICE on a mount point"

  mount point
    The place in the current system's directory structure where the filesystem will be attached
      The mount point can be anywhere on the system

  # mount
    -t 

    Shows the current filesystem status

    Ex output:
      proc on /proc type proc (rw,relatime)
      sysfs on /sys type sysfs (rw,relatime)
      /dev/xvda1 on / type ext4 (rw,noatime,data=ordered)
        *See common terminology above

        Each line corresponds to one currently mounted FS
        Ex:
        proc
          The device doesn't have to be real
        (rw, relatime)
          Mount options
        Type
          The filesystem type


    Ex: mounting a filesystem
      mount -t FS_TYPE DEVICE MOUNTPOINT
      Ex: mount -t ext4 /dev/xvda1 /

      Ex: You can also mount a FS by its UUID
        # mount UUID=7715e0d4-454d-4844-857b-5ef65f06b574 /

    mount options
      There are short options and long options.  
        Commands can be sometimes expressed in both formats.  Although, long options are preferred

        Short options
          Letters of the alphabet
          Can you really recognize the given option from a single letter?
        Long options
          start with -o and supply trailing keywords
          Ex:
            mount -t vfat /dev/hda1 /dos -o ro,conv=auto
              ro
                read only mode (same as -r)
              conv=auto
                If a text file contains the DOS newline character, the kernel will automatically convert it into UNIX style.
            
            Useful long options
              exec, noexec
                Enables/disables execution of programs on the FS.
      Additionally, the mount options can be categorized into general and filesystem specific
      
      general
        -t
          specifies the filesystem type that you want to mount
        -r
          Mounts the FS in read-only mode
            You don't need to do this if mounting a CD, etc.
        -n
          Ensures that mount doesn't try to update the system runtime mount database (/etc/mtab)
            The mount operation vails when it can't write to this file, which is important at boot because the root partition (that houses the mount database) is read-only at first

            If you're in single-user mode, this option is very handy because the mount database isn't available at that time.

      filesystem-specific
        pertain only to certain filesystem types

        Ex: -o norock
          Turns off Rock Ridge exts on an ISO 9660 file-system, but has no meaning for any other kind of FS


  umount
    Unmounting (detaching) a filesystem
    Ex: umount MOUNT_POINT/DEVICE


  Filesystem UUID (Universally Unique Identifier)
    A software standard
    UUID is a type of serial number, and each one is unique
      Ex: "7715e0d4-454d-4844-857b-5ef65f06b574"

    You can change the UUID of a FS
      You'd want to do this if copying a file system to somewhere else
        See tune2fs binary

    You can mount devices by the UUID

    Mounting Removable Media via UUID
      Ex: /media/8883-8888
      Many distros will mount removable media via its UUID.
        udevd
          handles the initial event for the device insertion



    The aforementioned method of mounting is predicated on device names (ex: /dev/xvda1)
      Remember, these names can change because they depend on how the order in which the kernel finds the device.
        This can be a problem for scripting, etc.

        To solve this problem, mount the FS by its UUID

        # blkid 
            aka Block ID
            View a list of devices and the corresponding FS UUIDs
            Some FSs, like FAT don't have a UUID that is in the traditional formatting







