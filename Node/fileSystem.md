Notes taken while reading:
	Node's API documentation

	File System
		All methods have a/sync forms
			With async methods, there is no guaranteed ordering.
				Utilize the async methods whenever possible.  The sync versions will block the entire process until they complete (this will halt all connections)


			If you need to do multiple operations on one file, utilize promises.

		You can utilize relative pathnames.
			These will be relative to process.cwd()

		For most methods that utilize a PATH, there's a alternative method that starts with "f" that utilizes an fd instead of a path.

		Error Handling
			Important when reading files to see if they were read successfully, etc.

			Most fs functions let you omit the callback argument.  If you do, a default callback is used that rethrows errors.
				To get a trace to the original call site, set the NODE_DEBUG ENV variable.
					Ex: 
						$ cat script.js
						function bad() {
						  require('fs').readFile('/');
						}
						bad();

						$ env NODE_DEBUG=fs node script.js
						fs.js:66
						        throw err;
						              ^
						Error: EISDIR, read
						    at rethrow (fs.js:61:21)
						    at maybeCallback (fs.js:79:42)
						    at Object.fs.readFile (fs.js:153:18)
						    at bad (/path/to/script.js:2:17)
						    at Object.<anonymous> (/path/to/script.js:5:1)
						    <etc.>

			Async methods
				always take an error object for its first argument
			
				If the operation was completed successfully the first argument will be null or undefined

					Ex: 
					var fs = require('fs');

					fs.unlink('/tmp/hello', function (err) {
					  if (err) throw err;
					  console.log('successfully deleted /tmp/hello');
					});

			Sync methods
				Exceptions are immediately thrown.
					Utilize try/catch to handle exceptions or allow them to bubble and be handled.

					Ex:
					var fs = require('fs');

					fs.unlinkSync('/tmp/hello');
					console.log('successfully deleted /tmp/hello');

			fs.link(srcpath, dstpath, callback)
				Equivalent of creating a hard link in linux
					ln originalName linkname

				If the original content is moved, the hard link still works

			fs.unlink(path, callback)

			fs.readlink(path, callback)
				callback
					args
						err
						linkString


			fs.realpath(path[, cache], callback)
				callback
					args
						err
						resolvedPath
				cache
					object literal of mapped paths that can be used to force a specific path resolution or avoid additional fs.stat calls for known real paths

					Ex:
					var cache = {'/etc':'/private/etc'};
					fs.realpath('/etc/passwd', cache, function (err, resolvedPath) {
					  if (err) throw err;
					  console.log(resolvedPath);
					});

			fs.rename(oldPath, newPath, callback)

			fs.rmdir(path, callback)

			fs.symlink(destination, path[, type], callback)
				Equivalent of creating soft links (sym links) in linux
					ln -s originalName linkName
						use symlinks when dealing with files on remote systems

				If the original content is moved, the link will NOT work

				type
					dir
					file
					junction

			fs.mkdir(path[, mode], callback)

			fs.truncate(path, len, callback)


			fs.open(path, flags[, mode], callback)
				flags
					"w", etc.
				callback
					args
						err
						fd
							file descriptor

			fs.read(fd, buffer, offset, length, position, callback)
				read fata from a file that's referenced by its fd

				buffer
					the buffer that the data will be written to

				offset
					offset in the buffer to start writing at

				length
					the number of bytes to read

				position
					where to begin reading from the file

					if null, the data will be read from the current file position

				callback
					args
						err
						bytesRead
						buffer

			fs.readdir(path, callback)
				Reads the contents of a directory
					callback
						args
							err
							files
								array of the names of the files in the directory

			fs.readFile(file[, options], callback)
				Ex: Asynchronously reads the entire contents of a file

				fs.readFile('/etc/passwd', function (err, data) {
				  if (err) throw err;
				  console.log(data);
				});

				In the options, if no encoding is specified, then the raw buffer id returned

			fs.watch()
				You can watch for changes on a given file
				Events:
					change
						Args:
							event
								Type of change
							filename
					error

				watcher.close()
					Stop watching for changes
			

			Stream Methods
				fs.createReadStream(path[, options])
					returns a new ReadStream object (readable stream)

					Ex: options
					{ flags: 'r',
					  encoding: null,
					  fd: null,
					  mode: 0o666,
					  autoClose: true
					}

						Options can include start and end values to read a range of bytes (instead of entire file)

						fd
							If specified, the path argument will be ignored and will utilize the given file descriptor instead.  Thus, no open event will be emitted

						autoClose
							if false, then the file descriptor won't be closed, even if there's an error.

							if true(default), on "error" or "end" the file descriptor will be closed automatically.

						mode
							sets the file mode if it's create

					Ex: For a 100 byte file (read the last 10 bytes)
						fs.createReadStream('sample.txt', {start: 90, end: 99})

				fs.createWriteStream(path[, options])
					Returns a WriteStream object (writable stream)

					the default options object
						{ flags: 'w',
						  defaultEncoding: 'utf8',
						  fd: null,
						  mode: 0o666 }

					Options may also include a "start" key to allow writing data at some position past the beginning of the file.

					Modifying a file rather than replacing it may require a flags mode of r+ rather than the default w mode.

					fd
						Similar idea to readStream

				fs.ReadStream
					Events
						open
							Args:
								fd
									Int file descriptor used by the ReadStream
				fs.WriteStream
					Events
						open
							Args:
								fd
									Int file descriptor used by the Writestream

					writeStream.bytesWritten
						The number of bytes written so far (not include data that's queued for writing)

			fs.access
				fs.access(path[, mode], callback)
					Path
						Tests a user's permissions for the file specified
					Mode
						The accessibility checks to be performed
							EX:
							fs.F_OK - File is visible to the calling process. This is useful for determining if a file exists, but says nothing about rwx permissions. Default if no mode is specified.
							fs.R_OK - File can be read by the calling process.
							fs.W_OK - File can be written by the calling process.
							fs.X_OK - File can be executed by the calling process. This has no effect on Windows (will behave like fs.F_OK).

					callback
						Args:
							error
								If any accessibility checks fail, the error argument will be populated
			
				EX: Can /etc/passwd be read and written by the current process?

				fs.access('/etc/passwd', fs.R_OK | fs.W_OK, function (err) {
				  console.log(err ? 'no access!' : 'can read/write');
				});

			fs.appendFile
				fs.appendFile(file, data[, options], callback)
					file
						Is a String or Int filename OR a file descriptor
				Async append data to a file

				Will create the file if it doesn't exist

				Any specified file descriptor has to have been opened for appending

				Note: Specified file descriptors will not be close automatically

			fs.chmod(path, mode, callback)

			fs.chown(path, uid, gid, callback)

			fs.close(fd, callback)
				Closes a given file descriptor

			fs.Stats
				Objects returned from fs.stat(), fs.lstat() and fs.fstat() and their synchronous counterparts are of this type.

				Has the following methods:
					stats.isFile()
					stats.isDirectory()
					stats.isBlockDevice()
					stats.isCharacterDevice()
					stats.isSymbolicLink() (only valid with fs.lstat())
					stats.isFIFO()
					stats.isSocket()

				Ex: 
					If you have a file and pipe its stats object into util.inspect

					util.inspect(stats)
						{ dev: 2114,
						  ino: 48064969,
						  mode: 33188,
						  nlink: 1,
						  uid: 85,
						  gid: 100,
						  rdev: 0,
						  size: 527,
						  blksize: 4096,
						  blocks: 8,
						  atime: Mon, 10 Oct 2011 23:24:11 GMT,
						  mtime: Mon, 10 Oct 2011 23:24:11 GMT,
						  ctime: Mon, 10 Oct 2011 23:24:11 GMT,
						  birthtime: Mon, 10 Oct 2011 23:24:11 GMT }