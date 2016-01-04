Notes:
SC = Similar command




Basic Linux Commands/variables/etc
$$
	A shell variable that holds the current shell's PID

					CONT



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

Permissions

	ls -l
		drwxr-xr-x  8 zroof  BFI\Domain Users  272 Oct  9 16:18 iTunes
		
		d = type
		rwx = user permissions (zroof)
		r-x = group permissions (BFI/Domain Users)
		r-x = other permissions

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

		You can apply the permisisons to certain categories
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
				r 	w 	x 	(always in this order)
				0	0	0	Value for off
				1	1 	1	Binary value for on
				4	2	1 	Base 10 value for on

			Octal Mode
				8 values (0 through 7) Hence the name
				Ex
					Octal	Binary   string   
					7		111		 rwx (4+2+1 which is the binary representation of 111)
					5		101 	 r-x (4+2)
				For setting preferences in chmod
					UGO order
						user, group, other
					Ex: chmod 750

				Most commonly used permissions

				Symbolic	Octal
				-rwx------ 	700
				-rwxr-xr-x 	755
				-rw-rw-rw- 	664
				-rw-rw---- 	660
				-rw-r--r-- 	644
					
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
										Ex: Base Permission 	666
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



