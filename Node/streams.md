Currently at - child.stdio <-- GO BACK

? - Double Check
These are the notes that I took while reading/taking:
Node Streaming Handbook
Node API Documentation
Stack Overflow Questions
Wikipedia

Overall Definitions
	File Descriptor
		In Unix and related computer operating systems, a file descriptor (FD, less frequently fildes) is an abstract indicator used to access a file or other input/output resource, such as a pipe or network connection. 

TODO:
	Blend this with stream info in basics.md

Streams
	The idea of dealing with things a chunk at a time.
	

	popular npm modules for stream creation
		through - Helps with pause/resume stream logic
		concat-stream


	.pipe() is the primary composition operator.
		this method also has a backpressure mechanism which allows us to throttle writes for slow consumers.

		Think of piping different streams together to make coding more elegant and modular

		All streams utilize .pipe() to "pipe" inputs to outputs

		src.pipe(dst)
			src - readable source stream
			dst - writable stream

			all pipe does is take the readable stream (src) and "pipes" the output to a destination writable stream (dst)

			.pipe(dst)
				returns dst for chaining
					the returned dst will become the read stream for the next pipe
						Thus, a.pipe(b).pipe(c).pipe(d)


	Example where streams would be effective


		var server = http.createServer(function (req, res) {
		    fs.readFile(__dirname + '/data.txt', function (err, data) {
		        res.end(data);
		    });
		});

		The problem is that this buffers the entire data.txt file into memory for every request.  Only after this process, does it write the results back to clients

	Utilizing Streams
		In the above example, with req and res are streams
			Thus, we can utilize fs.createReadStream() instead of fs.readFile()

		var server = http.createServer(function (req, res) {
		    var stream = fs.createReadStream(__dirname + '/data.txt');
		    stream.pipe(res);
		});

		There are two events that are usually used in streams
			data - Emitted for every buffered 'chunk' of binary that's ready to be transmitted

			end - When the stream has finished

				.pipe() takes care of listening for these 2 events
					So we utilize this in the above example
						The file will now be written to clients one chunk immediately after the chunk is read from disk

					Backpressure: If clients are on a high-latency connection, pipe will take account of this and node wont buffer chunks into memory needlessly.

		Compression in streams with oppressor
			Opressor abstracts away setting content-encoding

			Compression is for browsers that support gzip or deflate

			Ex:
				var oppressor = require('oppressor');

				var server = http.createServer(function (req, res) {
				    var stream = fs.createReadStream(__dirname + '/zach.txt');
				    stream.pipe(oppressor(req)).pipe(res);
				});
		Writable Streams
			events
				drain
					Called when a writable stream's interna
			Creating a writeable sream
				EX:

				var Writable = require('stream').Writable;
				var ws = Writable();
				ws._write = function (chunk, enc, next) {
				    console.dir(chunk);
				    next();
				};

				process.stdin.pipe(ws);

				$ (echo beep; sleep 1; echo boop) | node write0.js 
				<Buffer 62 65 65 70 0a>
				<Buffer 62 6f 6f 70 0a>

				If the readable stream you're piping from writes strings, they will be converted into Buffers unless you create your writable stream with Writable({ decodeStrings: false }).

				If the readable stream you're piping from writes objects, create your writable stream with Writable({ objectMode: true }).



				_write params
					chunk
						the data that is written by the producer
					enc
						Is a string which denotes the encoding

						This is only passed when a string has been written AND opts.decodeString is false

					next(err)
						Callback that tells the consumer that they can write more data

						err
							an optional error object, which emits an 'error' event on the stream instance.

				Writing to a writable stream
					process.stdout.write('yo/n'):

					or

					var fs = require('fs');
					var ws = fs.createWriteStream('message.txt');

					ws.write('yo');

					
					ws.end(' man\n');
						// To end the stream with an optional piece of data

					if you set opts.highWaterMark in the Writable constructor
						if there is more data than the buffer can hold (aka a 'backlog' of data, then calling .write() will return false.)
							If this is returned, then the drain event will indicate when it's appropriate to begin writing more data to the stream

				
		Duplex streams
			Readable/Writable
			Both ends of the stream engage in a two-way interaction
				Ex: Like a internet chat between 2 people

				Classic duplex pattern
					a.pipe(b).pipe(a)

		Classic streams
			The old interface for streams

			Whenever a stream has a "data" listener registered, it switches into "classic" mode and behaves according to the old API
				When you do this, you lose the benefits of the streams2 api

				Classic readable streams
					Utilize the readable-stream module to make your streams2 code compliant with node .8 and below

					Just event emitters that emit "data" events when they have data for their consumers

					emit an "end" event when they are done producing for their consumers

					.pipe() will check whether a classic stream is readable by checking if the following is true
						stream.readable

					Ex of a readable stream
						var Stream = require('stream');
						var stream = new Stream;
						stream.readable = true;

						stream.emit('data', "Yo");
						stream.emit('end', "That's all folks");

						stream.pipe(process.stdout):

				To read from a classic readable stream, simply register "data" and "end" listeners
					Ex:
						process.stdin.on('data', function(buf) {
							console.log(buf):
						});

						process.stdin.on('end', function() {
							console.log("I'm done mang!")
						})

						$ (echo beep; sleep 1; echo boop) | node classic1.js 
						<Buffer 62 65 65 70 0a>
						<Buffer 62 6f 6f 70 0a>
						I'm done mang!

				Class writable streams
					.write(buf)
					.end()
					.destroy()







		Transform streams
			Aka "through streams"

			A certain type of duplex stream (both readable and writable)
				In transform streams, the input is "transformed" by some process and then outputted
		
		Readable Streams
			Produce streams that can be fed into a writable, transform, or duplex stream

			ex:
				process.stdin.on('readable', function () {
				    var buf = process.stdin.read();

				    console.dir(buf);
				});

				$ (echo abc; sleep 1; echo def; sleep 1; echo ghi) | node consume0.js 
				<Buffer 61 62 63 0a>
				<Buffer 64 65 66 0a>
				<Buffer 67 68 69 0a>
				null

				// Notice the 0a which is the newline from echo

				When data is available on a stream, the 'readable' event fires and you can call .read() to pull in data from the buffer

				When the stream is finished, .read() returns null because there are no more bytes to fetch

				.read(n)
					You can fetch n bytes of data if you wish.
						This doesnt work for object streams

						If we read less bytes than there is in the buffer, those "leftovers" will be shown on the next buffer pull

						if n === 0
							it will output all of the data in the rest of the buffer



			Ex:
				var Readable = require('stream').Readable;

				var rs = new Readable;
				rs.push('beep ');
				rs.push('boop\n');
				rs.push(null);
					// Tells consumer of stream that rs is done outputting

				rs.pipe(process.stdout);

				$ node read0.js
					//beep boop

				We pushed content to rs BEFORE piping to process.stdout (out writable stream) and yet, our complete message was still written.
					This is because when you .push() to a readable stream, the chunks you push are buffered until a consumer is ready to read them.

					Although, it would be even better in many circumstances to avoid this type of buffering and only generate the data when someone asks for it.  We can push chunks on-demand if we define a ._read function on the stream
						Ex:

						var Readable = require('stream').Readable;
						var rs = Readable();

						var c = 1;
						rs._read = function (size) {
						    rs.push(c++);
						    if (c < 10)) rs.push(null);
						};

						rs.pipe(process.stdout);

						// size - how many bytes the consumer wants to read.  We can ignore this if we want.

						$ node FILE.js
						// 123456789

						Here, we only push the number when the consumer is ready to read.

						$ node FILE.js | head -c5
							in this case it would be the 'head stream' that would be requesting 5 bytes from as rs._read process.  For every byte sent, if we added in a delay to the code so that the operating system would send signals to close the pipe, then rs._read would be invoked 5 times (1 time for every byte requested).
								For this to FULLY work, we'd have to add some error handlers,etc. for dealing with external operating system pipes, but you get the main idea.
									If we interface with node streams the whole time, we dont have to deal with these nuances.
		Built-in streams
			processs
				process.stdin
					Readable stream that contains the standard system input stream for your program

					It is paused by default but the first time you refer to it .resume() will be called inplicitly on the next "tick" of the event loop (the current event has finished).

					If it is a tty (check with tty.isatty()) then input events will be line-buffered
						This functionality can be turned off by
							process.stdin.setRawMode(true);

				process.stdout
					Writable stream that contains the standard system output for your program

					.write()
						Will send data to stdout

				process.stderr
					Writable stream
					Contains the standard system error stream for your program

					.write()
						Will send data to stderr

			Child process
				Has its own distinct events

				Child processes and a parent process can communicate

					child.send(message[, sendHandle][, callback])
						sendHandle
							TCP server or socket object to another process. The child will receive the object as its second argument to the message event.

					var cp = require('child_process');

					var n = cp.fork(__dirname + '/sub.js');

					n.on('message', function(m) {
					  console.log('PARENT got message:', m);
					});

					n.send({ hello: 'world' });


					//sub.js script (below)

					process.on('message', function(m) {
					  console.log('CHILD got message:', m);
					});

					process.send({ foo: 'bar' });

					Ex: Sending a server object to a child.  For example, a child can handle certain ports (instead of a parent).  In this example, the server is shared between the parent and the child



						var child = require('child_process').fork('child.js');

						// Open up the server object and send the handle.
						var server = require('net').createServer();

						server.on('connection', function (socket) {
						  socket.end('handled by parent');
						});

						server.listen(1337, function() {
						  child.send('server', server);
						});



						//child.js (BELOW)
							process.on('message', function(m, server) {
							  if (m === 'server') {
							    server.on('connection', function (socket) {
							      socket.end('handled by child');
							    });
							  }
							});

					Ex: Sending a socket between a parent and 2 children.
					In this example, there are two children.  If the remote IP is 74.125.127.100 it will send it to one child process, if it isn't, it will go to another child process.

						var normal = require('child_process').fork('child.js', ['normal']);

						var special = require('child_process').fork('child.js', ['special']);

						// Open up the server and send sockets to child
						var server = require('net').createServer();
						server.on('connection', function (socket) {

						  // if this is a VIP
						  if (socket.remoteAddress === '74.125.127.100') {
						    special.send('socket', socket);
						    return;
						  }
						  // just the usual...
						  normal.send('socket', socket);
						});
						server.listen(1337);

						//child.js (below)

							process.on('message', function(m, socket) {
							  if (m === 'socket') {
							    socket.end('You were handled as a ' + process.argv[2] + ' person');
							  }
							});

						NOTE: Once a socket has been sent to a child then parent can no longer keep track of when it's destroyed
							Thus, use server.getConnections(callback)
								Asynchronously get the number of concurrent connections on the server. Works when sockets were sent to forks.

								callback utilizes "err" and "count" as arguments



				Is an EventEmitter

				Is possible

				Possible to stream data through a child's stdin, stdout, and stderr in a fully non-blocking way

					child.stdin
						If the child is waiting to read all its input, it will not continue until this stream has been closed via end().

						A Writable Stream

						If the child was not spawned with stdio[0] set to 'pipe', this will not be set

					Some programs use line-buffered I/O internally.
						If this is the case, the data you send to the child process may bot be immediately consumed

					Child streams can be shared with stdio streams of the parent process

					Can be seperate stream objects that can be piped to and from

				child.stdio
					(GO BACK)
					An array of pipe to the child processes

					A sparse array of pipes to the child process, corresponding with positions in the stdio option to spawn that have been set to 'pipe'. Note that streams 0-2 are also available as ChildProcess.stdin, ChildProcess.stdout, and ChildProcess.stderr, respectively.





				Creating a child processes
					These child processes are still whole new instances of V8. 
						Assume at least 30ms startup and 10mb memory for each new process.

					Async
						.exec()
							Utilized for invoking a child process and then terminating once the process is done.

							child_process.exec(command[, options], callback)

							Ex:
								var exec = require('child_process').exec,
								    child;

								child = exec('cat *.js bad_file | wc -l',
								  function (error, stdout, stderr) {
								    console.log('stdout: ' + stdout);
								    console.log('stderr: ' + stderr);
								    if (error !== null) {
								      console.log('exec error: ' + error);
								    }
								});

						.execFile()
							child_process.execFile(file[, args][, options][, callback])

							This is similar to child_process.exec() except it does not execute a subshell but rather the specified file directly. This makes it slightly leaner than child_process.exec(). It has the same options.


							







						.spawn()
							child_process.spawn(command[, args][, options])	
								options.detached
									Allows child_processes to outlive their parent (aka start a long running process)

									On linux, if set to true, the child process will be made the leader of a new process group and session



									By default, the parent will wait for the detached child to exit.  To prevent this, utilize child.unref()
										The parent's event loop will now NOT include the child in its reference count

									When utilizing this option to start

									Example of detaching a long-running process and redirecting its output to a file:

									 var fs = require('fs'),
									     spawn = require('child_process').spawn,
									     out = fs.openSync('./out.log', 'a'),
									     err = fs.openSync('./out.log', 'a');

									 var child = spawn('prg', [], {
									   detached: true,
									   stdio: [ 'ignore', out, err ]
									 });

									 child.unref();
									 	// The process will not stay running in the background after the parent exits unless it is provided with a stdio config that is not connectioned to the parent.  If the parent's stdio is inherited, the child will remain attached to the controlling terminal.

									options.stdio
										Utilized to specify the communications between a parent and child process

										A 3 member array where each index corresponds to an file descriptor in the child.
											[stdin, stdout, stderr]
												In each of the above indexes, you can attach the following strings/objects from below.

											'Pipe'
												Create a pipe between the child process and the parent process.

												This is the default value

												 The parent end of the pipe is exposed to the parent as a property on the child_process object as ChildProcess.stdio[fd]. Pipes created for fds 0 - 2 are also available as ChildProcess.stdin, ChildProcess.stdout and ChildProcess.stderr, respectively.

											'ipc'
												Inter process communication

												Create an IPC channel for passing messages/file descriptors between parent and child. A ChildProcess may have at most one IPC stdio file descriptor. Setting this option enables the ChildProcess.send() method. If the child writes JSON messages to this file descriptor, then this will trigger ChildProcess.on('message'). If the child is an Node.js program, then the presence of an IPC channel will enable process.send() and process.on('message').

											'ignore'
												Do not set this file descriptor in the child. 

												Node will always open fd 0 - 2 for the processes it spawns. When any of these is ignored Node.js will open /dev/null and attach it to the child's fd.

											Stream obj
												Share a readable or writable stream that refers to a tty, file, socket, or a pipe with the child process

											Positive INT
												The integer value is interpreted as a file descriptor that is is currently open in the parent process. It is shared with the child process, similar to how Stream objects can be shared.

											null, undefined
												Use the default value (aka 'pipe')

											EX:
												var spawn = require('child_process').spawn;

												// Child will use parent's stdios
												spawn('prg', [], { stdio: 'inherit' });

												// Spawn child sharing only stderr
												spawn('prg', [], { stdio: ['pipe', 'pipe', process.stderr] });

												// Open an extra fd=4, to interact with programs present a
												// startd-style interface.
												spawn('prg', [], { stdio: ['pipe', null, null, null, 'pipe'] });

						Ex:
							var spawn = require('child_process').spawn,
							    grep  = spawn('grep', ['ssh']);

							grep.on('close', function (code, signal) {
							  console.log('child process terminated due to receipt of signal ' + signal);
							});

							// send SIGHUP to process
							grep.kill('SIGHUP');






							Example: A very elaborate way to run 'ps ax | grep ssh'

							var spawn = require('child_process').spawn,
							    ps    = spawn('ps', ['ax']),
							    grep  = spawn('grep', ['ssh']);

							ps.stdout.on('data', function (data) {
							  grep.stdin.write(data);
							});

							ps.stderr.on('data', function (data) {
							  console.log('ps stderr: ' + data);
							});

							ps.on('close', function (code) {
							  if (code !== 0) {
							    console.log('ps process exited with code ' + code);
							  }
							  grep.stdin.end();
							});

							grep.stdout.on('data', function (data) {
							  console.log('' + data);
							});

							grep.stderr.on('data', function (data) {
							  console.log('grep stderr: ' + data);
							});

							grep.on('close', function (code) {
							  if (code !== 0) {
							    console.log('grep process exited with code ' + code);
							  }
							});

						.fork()
							Does not clone the current process

							require('child_process').fork()
								This is a special case of the child_process.spawn() functionality. In addition to having all the methods in a normal ChildProcess instance, the returned object has a communication channel built-in. See [child.send(message, [sendHandle])][] for details.

								child_process.fork(modulePath[, args][, options])

									modulePath
										The module to run in the child
					
					Synchronous Process Creation
						As these methods are synchronous, they WILL block your event loop.  This will pause execution of your code until the spawned process exits.

						Good for scripting tasks, loading/processing of an app config at startup

						Note the "Sync" (Synchronous) part of the method

						.execFileSync
							child_process.execFileSync(file[, args][, options])

						.spawnSync
							child_process.spawnSync(command[, args][, options])

				










	













