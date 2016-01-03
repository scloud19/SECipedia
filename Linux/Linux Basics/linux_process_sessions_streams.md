Processes/Sessions
  
  Processes
    Use I/O streams to read/write data.

    Processes read data from input streams and write data to output streams

    Each process on the system has a PID (process ID)

          




  Streams

    Standard Input (stdin)
      Streams can be very flexible as the source of an input stream can be a file, device, terminal, output stream of another process.

      For most commands, if you don't specify an input file, it will read from stdin (thus it will interact like cat does-as seen below)

      stdin redirection

        <
          Channels a file to a program's stdin

          Ex: cat < /etc/passwd

          This isn't very common because most unix commands accept filenames as args
            Ex: cat /etc/passwd


    Standard Output (stdout)
      Kernel gives each process a standard output stream
        This is where the process writes its output

      Some commands send output only to stdout (thus you see it on your screen... in most cases), but some commands can send items directly to files.

      $ COMMAND > file
        Redirects stdout from terminal into a file

        file
          Shell will create the file if it doesn't already exist.

          If the file does exist, the shell will erase the original.
            To turn this off in bash
              $ set -C
        
        >
          redirection character
            >> is also a redirection character

      $ COMMAND >> file
        Appends the output to a file instead of overwriting it

        This is very helpful if you're executing multiple commands and want to aggregate logging, etc.

      Piping ( command | command )
        To send the standard output of a command to the standard input of another        

        

    Stream IDs
      1
        stdout (the default)
      2
        stderr

    Standard Error(stderr)
      Output stream for diagnostics and debugging.

      When you see an error on the console; its from stderr

      Error messages
        ls /not_a_directory
          -> ls: /not_a_directory: No such file or directory

        3 components to error messages
          ls
            The program name, but this isn't always shown.  So if you're writing shell scripts, watch out.

          /not_a_directory
            The file name.

          No such file or directory
            The error.

        In general; always address the first error message.  Usually there's a chain reaction that's based on the first problem.





      Ex: redirect stderr to file 'err' and stdout to file 'out'
        ls /yoyo > out 2> err

        2>
          2 is the stderr stream Id in the redirection

      >&
        Redirect stderr and stdout to the same place

        Ex: send stderr and stdout to file 'all_output_streams'
          ls /yoyo > all_output_streams 2>&1

      Common error messages
        Not a directory, Is a directory
          Occurs when you try to use a file as a directory or a directory as a file.
          Ex:
            touch yo
            touch yo/man
            -> touch: a/b: Not a directory

        Operation not permitted
          Can occur when you try to kill a process that you don't own.

        Segmentation fault
          Segmentation fault
            the programmer who wrote the program messed up.  The program tried to access a part of memory that it was not allowed to touch, and the OS killed the process.

        Bus error
          Similar idea to the segmentation fault.  

          Means that the program tried to access some memory in a way that it shouldnt.

          Segmentation fault/Bus error
            If you're getting one of these errors, you might be giving a program some input that it wasn't expecting.




    Control-D
      Stops the current standard input entry (EOF is sent)

      This isn't the same as Control-C, which terminates the program (regardless of input/output)

    $ cat
      If you don't specify a filename, cat implements an interactive behavior because of streams.

      Because you didn't specify an input filename, cat read from the standard input stream provided by the kernel rather than a stream connected to a file.
        In this example, the standard input was connected to the terminal


	A process group is a collection of related processes which can all be signalled at once.

	A session is a collection of process groups, which are either attached to a single terminal device (known as the controlling terminal) or not attached to any terminal.

	Sessions are used for job control: one of the process groups in the session is the foreground process group, and can be sent signals by terminal control characters. You can think of a session with a controlling terminal as corresponding to a "login" on that terminal. (Daemons normally disassociate themselves from any controlling terminal by creating a new session without one.)

	e.g. if you run some_app from the shell, the shell creates a new process group for it, and makes that the foreground process group of the session. (some_app might create some child processes; by default they will be part of the same process group.) If you then press ^Z, some_app's process group is signalled to stop it; and the shell's process group is switched to be the foreground process group again. Then e.g.bg %1 would start some_app's process group again, but keep it running in the background.
		http://stackoverflow.com/questions/6548823/use-and-meaning-of-session-and-process-group-in-unix

