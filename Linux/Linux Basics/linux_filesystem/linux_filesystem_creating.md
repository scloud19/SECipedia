Only do this once you've partitioned

mkfs
  Utility that can create many kinds of file systems

  Ex making a ext4 on a dev/sdf4 parition
    mkfs -t ext4 /dev/sdf2
      Output
        prints diagnostic output as it works, including:
          superblock
            A key component at the top level of the FS database.
              It's important that mkfs creates a number of backups
            Record a few of the superblock backup numbers
              This will help you recover the superblock if the disk fails
