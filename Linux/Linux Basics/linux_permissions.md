Permissions

  ls -l
    
    Ex: drwxr-xr-x  8 zroof  BFI\Domain Users  272 Oct  9 16:18 iTunes
    
      drwxr-xr-x = file's mode
        Each read/write/execute permission slot is sometimes refered to as a "permission bit".
          Sometimes you might hear "the read bit is set for the user"

      d = file's type, in this case; a directory. '-' means regular file.
      rwx = user permissions
        These are for the user that owns the file (zroof)
      r-x = group permissions (BFI/Domain Users)
      r-x = other permissions (aka world permissions)
        What everyone else on the system (that wasn't specified by the user/group) can do.

      After the file's type, you can think of UGO (the car).
        user, group, other

    Sym/ Def (for the beginning of the string)

    - regular file
    d directory
    l sym link

    Sym/ Def (if a file)
    r read file
    w change contents
    x execute as a program

    Sym/Def (if a directory)
    r allows file names in the dir to be read
    w allows entries to be modified within the dir
      You can edit files from within the dir
    x Allows access to contents and metadata for entries (last modification time, etc)

      Directory permissions
        Permissions on a directory can effect the files in the directory

        If you have problems with permissions, look at the main file, and then work your way up to root.  
          Ex: You don't have execute permissions on the directory, thus you couldn't execute files inside of that directory

    
    s 
      Some executable files have an 's' in the user permissions listing instead of an x.
        This indicates that the executable is setuid, which means that when you run the program, it runs as though the file owner is the user; and not as you.

        setuid bit
          Means programs use this bit to run as root for privilege reasons.

          Ex on some systems: passwd
            This program needs to change the /etc/passwd file

    CURRENTLY AT

    You can apply the permissions to certain categories
    Sym/ Def
    u user that owns the file
    g Group
    o Other (not the file owner or in the given group)
    a All

    Groups
      Every user is in at least one group
      groups
        command displays a user's group

      When you create a file, the files belong to your primary group (first group)
      
      chgrp GROUPNAE FILE
        changes the group of a file

    chmod _MODE_ _FILE_ - change mode command
      ex: chmod g+wx,u+r z.data
        seperate groups by comma

        chmod o= z.data
          removes all permissions

      Mode:
      ugoa - user category (user,group,other, all)
      +-= - Add, subtract, or set permissions
        = or "set permission" sets the permissions to exactly what you specifiy
      rwx - Read, Write, Execute

      You can utilize numeric mode as well
        r   w   x   (always in this order)
        0 0 0 Value for off
        1 1   1 Binary value for on
        4 2 1   Base 10 value for on

      Octal Mode
        8 values (0 through 7) Hence the name
        Ex
          Octal Binary   string   
          7   111    rwx (4+2+1 which is the binary representation of 111)
          5   101    r-x (4+2)
        For setting preferences in chmod
          UGO order
            user, group, other
          Ex: chmod 750

        Most commonly used permissions

        Symbolic  Octal
        -rwx------  700
        -rwxr-xr-x  755
        -rw-rw-rw-  664
        -rw-rw----  660
        -rw-r--r--  644
          
        File Creation Mask
          File creation mask determines default permissions
            this is usually set by the sysadmin
              This can be changed on a per user basis using umask
                special modes of umask
                  setuid
                  setgid
                  sticky
              umask [-S] OCT_MODES
                -S symbolic mode
                  umask -S
                    You can see current umask displayed symbolically
                This is the opposite of chmod.
                  chmod's 7 would be rwx
                  in umask this is --- (no privs)
                Finding umask's OCT_MODES (estimation)
                  If a MODE is negative then you can't use this technique
                    Ex: Base Permission   666
                      umask              -007
                      Creations Perm.    66-1
                  Simply subtract the octal umask modes from the base permission
                  Files
                      Base permission 666
                      Subtract Umask -022
                    Creation permission 755
                  Directories
                      Base permission 777
                      Subtract Umask -002
                    Creation permission 755
            umask 0022 is the same as 022
              this works the same for chmod
          By default (if no mask is given)
            777 for directories
            666 for files

        Cautions
          If you are trying 777/666 you're probably doing it wrong

          777
            Everyone has access to that file or directory
            People can potentially input bad code into file and execute it
          If multiple people need access to a file, consider putting them into a specific group



