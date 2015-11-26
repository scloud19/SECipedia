net module
	Provides an asynchronous network wrapper.

	Contains functions for creating servers and clients (called streams)

	require('net')

	net.Server
		Is an EventEmitter
		events
			close
				Emitted when the server closes.

				If connections exist, this event is not emitted until all connections are eneded

			connection
				Emitted when a new connection is made.
					args
						socket
							is an instance of net.Socket
			error
				Emitted when an error occurs

				The close event will be called after this event

			listening
				Emitted when the server has bound after calling server.listen

		server.address()
			Ex of returned object
			{ port: 12346, family: 'IPv4', address: '127.0.0.1' }

			Ex:
			var server = net.createServer(function (socket) {
			  socket.end("goodbye\n");
			});

			// grab a random port.
			server.listen(function() {
			  address = server.address();
			  // Always call after 'listening' event has been emitted

			  console.log("opened server on %j", address);
			});

		server.close([callback])
			Stops the server from accepting new connections
				However, current connections are kept

			The server is formally closed when all connections are ended and the server emits a 'close' event

			callback
				called once the 'close' event occurs

		server.getConnections(callback)
			Async get the number of connections on the server

			This even works when sockets were sent to forks

			callback
				args
					err
					count

		server.listen(handle[, callback])
			handle
				Is an object that can be set to either a server or socket (anything with an underlying _handle memeber), or a {fd: <N>} object

				The server will accept connections on the specified handle, but it is presumed that the file descriptor/handle has already been bount to a port or domain socket

				Async in nature

				When the server has been bound, 'listening' event will be emitted.  

				callback
					will be added as a listener for the 'listening' event

		server.listen(options[, callback])
			options
				Properties:
				port (Number) - Optional.
				host (String) - Optional.
					Ex: localhost
				backlog (Number) - Optional.
					Max length of the queue of pending connections
				path (String) - Optional.
					Can specify a unix socket
				exclusive (Boolean) - Optional.
					 If false (default)
					 	cluster workers will use the same underlying handle
					 	
					 	This allows connection handling duties to be shared

					 If true
					 	handle isn't shared

					 	port sharing results in an error
		
		server.listen(path[, callback])

			Starts a local socket server listening for connections on the given path

			Aside: A Unix domain socket or IPC socket (inter-process communication socket) is a data communications endpoint for exchanging data between processes executing on the same host operating system. 

			path
				On UNIX, the local domain is usually known as the UNIX domain. The path is a filesystem path name. It is subject to the same naming conventions and permissions checks as would be done on file creation, will be visible in the filesystem, and will persist until unlinked.

			server.listen(port[, hostname][, backlog][, callback])
				backlog
					Maximum length of the queue of pending connections
						The actual length is determined by your OS through sysctl settings such as tcp_max_syn_backlog and somaxconn on linux

				When the server has been bound, the 'listening' event will be emitted.

				callback
					will be added as a listener for the 'listening' event

				EADDRINUSE
					Error if the port is in use

					Ex of retry code
					server.on('error', function (e) {
					  if (e.code == 'EADDRINUSE') {
					    console.log('Address in use, retrying...');
					    setTimeout(function () {
					      server.close();
					      server.listen(PORT, HOST);
					    }, 1000);
					  }
					});

			server.MaxConnections
				Set the max connections

				Do not use this once a socket has been sent to a child with child_process.fork()

			server.unref()
				Will allow the program to exit if this is the only active server in the event system.

				returns 'server' for chaining

			server.ref()
				Opposite of unref()

				Calling 'ref' on a server that was previously 'unref'd will keep the program running
					This assumes that the server is the only server left

			net.Socket
				a TCP/local socket abstraction

				Implements a duplex stream interface

				Sockets can be created by the user and used as a client (with connect() ) OR they can be created by Node and passed to the user through the 'connection' event of a server.

				new net.Socket([options])
					Constructor for a new socket object

					object defaults
						{ fd: null,
						  allowHalfOpen: false,
						  readable: false,
						  writable: false
						}

						fd
							allows you to specify the existing file descriptor of the socket

						readable/writable
							If true, allows you to allow reads and/or writes on this socket
								This only works when fd is passed

						allowHalfOpen
							refer to createServer() and 'end' event

				net.Socket is an instance of EventEmitter
					events
						close
							Emitted once the socket if fully closed

							args
								had_error
									boolean - Transmission error
						connect
							Socket connection was successfully established

						data
							args
								data
									buffer object OR String

									Encoding of data is set by socket.setEncoding()

									If there are no listeners for a sockets data event, DATA WILL BE LOST

						drain

