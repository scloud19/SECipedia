
Be aware that bash writes the history of the current session to the disk on exit. If you log out of your instance after deleting ~/.bash_history, and then log back in, you will find that ~/.bash_history has been re-created and contains all of the commands executed during your previous session.

Other programs besides bash also write histories to disk, Use caution and remove or exclude unnecessary dot-files and dot-directories.