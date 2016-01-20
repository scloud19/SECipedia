Sector
  A subdivision of a track on a magnetic or optical disk (https://en.wikipedia.org/wiki/File:Disk-structure2.svg).
    The sector is the minimum storage unit of a hard drive.  All files must occupy at least one sector, regardless of the file's actual size.  However, in practice, OSs typically operate on blocks of data, which may span multiple sectors.

    Each sector stores a fixed amount of user-accessible data, usually 512 bytes for HDs and 2048 bytes for CD-ROMs and DVD-Roms.  Newer HDs use 4096-byte sectors, which are known as the Advanced Format (AF)

    Physical Sector
      Consists of 3 basic parts
        Sector header, the data area, and the error-correcting code (ECC field)

        Sector header
          Contains information used by the drive and controller.  Ex of information stored, 'address identification'
            Address identification is used to ensure that the mechanics of the drive have positioned the read/write head over the correct location.

          Data Area
            The recorded user data

          ECC field
            Contains codes based on the data field.  These are used to check and possibly correct errors that may have been introduced into the data.


