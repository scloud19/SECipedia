mounting
  The process of attaching a file system

  Common terminology
    "mount a DEVICE on a mount point"

  mount point
    The place in the current system's directory structure where the filesystem will be attached
      The mount point can be anywhere on the system

  mount
    binary

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

  umount
    Unmounting (detaching) a filesystem
    Ex: umount MOUNT_POINT


  Filesystem UUID (Universally Unique Identifier)
    The aforementioned method of mounting is predicated on device names (ex: /dev/xvda1)
      Remember, these names can change because they depend on how the order in which the kernel finds the device.
        This can be a problem for scripting, etc.

        To solve this problem, mount the FS by its UUID





