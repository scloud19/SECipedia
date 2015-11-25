Processes/Sessions

	A process group is a collection of related processes which can all be signalled at once.

	A session is a collection of process groups, which are either attached to a single terminal device (known as the controlling terminal) or not attached to any terminal.

	Sessions are used for job control: one of the process groups in the session is the foreground process group, and can be sent signals by terminal control characters. You can think of a session with a controlling terminal as corresponding to a "login" on that terminal. (Daemons normally disassociate themselves from any controlling terminal by creating a new session without one.)

	e.g. if you run some_app from the shell, the shell creates a new process group for it, and makes that the foreground process group of the session. (some_app might create some child processes; by default they will be part of the same process group.) If you then press ^Z, some_app's process group is signalled to stop it; and the shell's process group is switched to be the foreground process group again. Then e.g.bg %1 would start some_app's process group again, but keep it running in the background.
		http://stackoverflow.com/questions/6548823/use-and-meaning-of-session-and-process-group-in-unix