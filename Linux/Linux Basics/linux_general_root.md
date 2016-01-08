su
  Can enter the root shell

  Disadvantages
    You have no record of system-altering commands and you don't know which users performed these commands.

    No access to your local shell env (i.e. your relevant configs, etc.)

sudo
  When running a command as sudo, it will be logged by the syslog service under the local2 facility.

  /etc/sudoers
    visudo
      Use to edit sudoers.
      This command checks for file syntax errors after you save the file.

    A file that specifies who can run sudo commands.

    Ex:
      User_Alias ADMINS = userA, userB
      # Specify a group of users

      ADMINS ALL = NOPASSWD: ALL
      # Grants the privs to the above group
      # Users in ADMINS can use sudo to execute commands as 
      # root.

      # The first ALL means "any host", if you have more than one machine, you can focus your permissions on that.

      # The second ALL means "any command" as root

      root  ALL=(ALL) ALL

      # Means that the superuser may also use sudo to run any command on any host.

      # The extra (or third ALL) means that the superuser may also run commands as any other user

      # ADMINS ALL = (ALL) NOPASSWD: ALL
      # Use this, instead of the aforementioned ADMINS role, to allow any ADMIN user the ability to run commands as any user on the system.