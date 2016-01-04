Notes:
SC = Similar command




Basic Linux Commands/variables/etc
$$
	A shell variable that holds the current shell's PID



CTRL-Z
	Put process into background

	fg
		to get it back

CTRL-L
	Refresh screen

CTRL-R
	If a program is reading from STDIN, this will usually redraw/refresh the current line.

groups
	See what group/s you're in


ps aux
  -> 
    USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
    root         1  0.0  0.2  19612  2624 ?        Ss    2015   0:02 /sbin/init

    Common fields
      TTY
        The terminal device where the process is running.
      STAT
        The process status.  I.e., what is the process doing and where its memory resides.

        S
          The process is sleeping
        R
          The process is running

        ps(1) gives a description for all of the symbols.

      TIME
        The amount of CPU time in minutes and seconds that the process has used.
      COMMAND
        Is obvious but be aware that a process can change this field from its original value
  Switches (run them without the '-')
  	x
  		show all of your running processes
  	ax
  		show all processes on the system (not just yours)
  	u
  		Include more detailed information
  	w
  		show the fill command names, not just what fits on a line

  To get detailed information on a process, pass its PID to ps
tr
	Do a transformation

	Ex:
		head /etc/passwd | tr a-z A-Z
			invert casing

chsh
	Change shell

passwd
	change your password

sort
	quickly puts the lines of a text file in alphabetic order

	-n 
		if the lines start with numbers and you want to list numerically

	-r
		reverse the order of the sort

touch
	creates a file
		If this is already present, updates the timestamp

diff -u file1 file2
	Find the differences between 2 text files

file filename
	Have the system guess what kind of file FILENAME is.

grep
	Prints the lines from a file or input stream that match an expression.

	egrep (grep -E) is a more powerful version of grep

	grep understands regExs

	Ex: Print the lines in /etc/passwd that contain the text root
		$ grep root /etc/passwd

	Ex: Find all lines that contain "root" in /etc/
		$ grep root /etc/*

		Very helpful because it lists what file the match was found in

		Important switches
			-i
				case-insensitive matches

			-v
				Prints all lines that DON'T match the search expression

			


Displaying contents of files
	cat FILE - display the contents of a file
	less FILE - browse through a textfile
		Use space to advance to next page
		enter - advance 1 line
		q - leave file
			commands are based on vi
	head FILE - output the top portion of a file/stream
		-8 shows 8 lines (applies for tail too)
			Can change this number to whatever you want

		+9
			Print all lines starting at line 9
	
	tail FILE - output the ending of file/stream
		-f FILENAME
			displays data being written in real time
ls
	-l - Long listing
	EX: drwx------  20 zroof  BFI\Domain Users   680 Aug 17 10:14 Applications
		permissions, links to file, owner, group, number of bytes in file, last modification time
	-a Show hidden files as well
	-t List files by time
	-r Reverse
	-R List all files recursively
		SC: tree
			Shows visual tree structure of files
			-d list directories only
			-C Colorize output
	-F reveal file types
		Appended to the end of file:
			/ Directory
			@ Link
			* Executable
	--color 
		Colorize the output
cp
	copies files
	
	Ex: cp file1 file2
		file1 is copied to file2

	Ex: cp file1 file2 file3 dir
		Copies all of the files to the directory(dir)

mv
	Moves/renames a file

	mv file1 file2

	mv file1 file2 DIR
		Moves all files to DIRectory


cd
	without any arguments changes you to $HOME
	cd -
		Changes to previous directory
pwd - print working directory
	-P
		Sometimes sym links can obfuscate the full pwd.  If so, add this flag
cat - Concatenates and displays files
echo
	Very helpful for finding expansions of shell globs (i.e., *) and variable $HOME, etc.

	echo *
		prints a list of files in the CWD
man
exit - Exits the shell or your current session
clear
pkill PATTERN
	kill all processes with a given pattern
		ex: pkill mongod
file FILENAME - what kind of file is this?
	
which - Locates a command
./COMMAND - Execute command in this directory
mkdir [-p] DIR_NAME - Create directory
	-p allows us to make parent directories if nesting is specified

rm -rf DIR_NAME - Recursively removes dir and files in it

find [path..] [expression]
	Recursively finds files in path that match the expression 

	path..
		is the directory to start searching from

	find options
		You can utilize multiple options at once
		-name PATTERN
			finds files and directories that match pattern
			Ex;
				find /bin -name *v
		-iname PATTERN
			Like -name, but ignores case

		-ls performs an ls on each of the found items

		-exec COMMAND {} /;
			Run command against all the files that are found
locate PATTERN
	Ex: locate sales
		Gives all files/directories with sales in the name
	Faster than the find command

	Queries an index that might be a day old (so there is a lag)
 

Environment Variables
	Storage location that has a name and a value
	Usually uppercase
	Access contents by executing
		echo $VAR_NAME

	$OLDPWD
		Previous PWD

	$PATH
		Controls the command search path
			This is a list of directories that are evaluated when looking for the binary of a command.
			Is evaluated from left-to-right

Help
	Run command with --help or -h
	man -k SEARCH_TERM
		Once a program is found do:
			man PROG_NAME

Hidden Files
	Sometimes called "dot files"
		start with a .
		Use ls -a to see ALL files

Working with spaces in the CLI
	Encapsulate the entire name in quotes
	Use \ to escape spaces

