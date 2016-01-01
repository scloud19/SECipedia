
Be aware that bash writes the history of the current session to the disk on exit. If you log out of your instance after deleting ~/.bash_history, and then log back in, you will find that ~/.bash_history has been re-created and contains all of the commands executed during your previous session.

Other programs besides bash also write histories to disk, Use caution and remove or exclude unnecessary dot-files and dot-directories.

If you allow non-root users to modify any files that root either executes or writes on then you open your system to root compromises. For example, someone could replace the httpd binary so that the next time you start it, it will execute some arbitrary code. If the logs directory is writeable (by a non-root user), someone could replace a log file with a symlink to some other system file, and then root might overwrite that file with arbitrary data. If the log files themselves are writeable (by a non-root user), then someone may be able to overwrite the log itself with bogus data.

The OS passes all of your shell's environment variables to programs that the shell runs
  Think of a bad program that is trying to capture these variables (which can contain major auth information).
    Can you lock down these env variables to specific programs?