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