view FILE
	starts vim in read-only mode

Always in 1 of 3 modes
	command mode
		default mode
		esc - Will place you in this mode

		can search text, delete text, copy/paste, etc.
	insert mode
		invocation of mode
			i - insert at cursor position
			I - Insert at the beginning of the line
			a - Append after the cursor position
			A - Append at the end of the line
	line mode
		commands start with ':'
		:w - saves the file
		:w! - force save
		:q - quit
		:q! - quit without saving changes
		:x - write and then quit

		:n - Positions the cursor at line n
		:$ - Positions the cursor on the last line

		:set nu - Turn on line numbering
		:set nonu - Turn off line numbering

		:help [subcommand] - Get help