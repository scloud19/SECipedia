Processes/Sessions
  
  Processes
    Use I/O streams to read/write data.

    Processes read data from input streams and write data to output streams

  Streams

    Standard Input (stdin)
      Streams can be very flexible as the source of an input stream can be a file, device, terminal, output stream of another process.

      For most commands, if you don't specify an input file, it will read from stdin (thus it will interact like cat does-as seen below)

    Standard Output (stdout)
      Kernel gives each process a standard output stream
        This is where the process writes its output

      Some commands send output only to stdout (thus you see it on your screen... in most cases), but some commands can send items directly to files.

      $ COMMAND > file
        Redirects stdout from terminal into a file
        
        >
          redirection character


    Standard Error 
      Another stream

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
