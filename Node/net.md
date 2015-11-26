net module
	Key Takeaways
		Check the bufferSize associated with the socket.  If the buffer is too large, throttle with pause() and resume()
			See socket.bufferSize for more information

		Sockets don't initially have a timeout on the connection.
			Make sure to implement a .setKeepAlive and .setTimeout to gracefully handle disconnected sockets that weren't handled gracefully

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
							Emitted when the write buffer becomes empty

							Can utilize to throttle uploads

							Also look at the returned value of socket.write()

						end
							Emitted when the other end of the socket sends a FIN packet

							
							allowHalfOpen
								option when creating the server

								false (default)
									The socket will destroy its fd once it has written out its pending write queue.
								true
									The socket will not automatically end() its side, which allows the user to write arbitrary amounts of data
										If utilized, the user must now .end() their side of the socket manually once finished
						error
							args
								Error object

						timeout
							This is only to notify that the socket has been idle.
								The user must manually close the connection

							socket.setTimeout() value is utilized

						socket.address()
							Ex: 
							{ port: 12346, family: 'IPv4', address: '127.0.0.1' }

						socket.bufferSize
							The computer can't always keep up with the amount of data that's written to a socket (maybe there's too much latency, etc.)
								In this case, Node will automatically queue up the data written to a socket and send it over the wire when it's possible
									Node implements this by polling the socket's fd for being in a 'writable' state

									The problem with this internal buffering is that memory consumption can grow out wildly.
										socket.bufferSize shows the number of characters currently buffered (and pending writes)
											The number of characters is roughly equal to the number of bytes to be written

											If this number gets too large, we should throttle the data flows with .pause() and .resume()
						
						socket.bytesRead
							The amount of received bytes

						socket.bytesWritten
							The amount of bytes sent

					socket.connect(options[, connectListener])
						Opens the connection for a given socket

						Normally this method isn't needed as net.createConnection opens the socket.

						This method is primarily for implementing a custom Socket

						The 'connect' event is emitted when the socket is established, this event will invoke connectListener

						If a connection can't be established, 'error' will be emitted with the exception

						For TCP sockets, the options object needs

							port: Port the client should connect to (Required).

							host: Host the client should connect to. Defaults to 'localhost'.

							localAddress: Local interface to bind to for network connections.

							localPort: Local port to bind to for network connections.

							family : Version of IP stack. Defaults to 4.

							lookup : Custom lookup function. Defaults to dns.lookup.

							path: Path the client should connect to (Required for Local Domain Sockets)
								If utilizing this, you don't need to specify the port and host values/keys

					socket.destroy()
						No more I/o activity will occur on this socket

						Only utilize this method when error handling

					socket.end([data][, encoding])
						Half-closes the socket
							Thus, a FIN packet is sent.

							Remember, the server can still send data.

						data
							Equiv
								socket.write(data, encoding)
								socket.end()

					socket.localAddress
						The local IP that the remote client is connecting on.
							Ex:
							192.168.1.1

					socket.localPort

					socket.pause()
						Pauses the reading of data.
							'data' events will not be emitted.
						Useful to throttle an upload

					socket.resume()
						Resumes reading after a call to pause()

					socket.ref()
					socket.unref()
						Similar idea to server.ref()/.unref() but replace server for socket in the definitions

					socket.remoteAddress
						String representation of the remote IP
							EX: 
							74.125.127.100
							2001:4860:a005::68

					socket.remoteFamily
						Ex: 'IPv4'

					socket.remotePort

					socket.setEncoding([encoding])
						Set the encoding of the socket as a Readable Stream

					socket.setKeepAlive([enable][, initialDelay])
						Enable/disable keep-alive functionality

						This setting has to do with making sure that a longstanding connection is still open

						initialDelay (in ms)
							The delay between the last data packet recieved and the first keepalive probe

						This function is utilized in conjunction with socket.setTimeout

					socket.setTimeout(timeout[, callback])
						net.Socket doesn't have a timeout by default.

						When the timeout is triggered the socket will receive a 'timeout' event but the connection will still be "live"
							We must manually implement an end()/destroy()
								We can do this in the callback
					
					socket.setNoDelay([noDelay])
						By default, TCP connections buffer data before sending it off.
							Setting noDelay to true will immediately fire off data each time socket.write() is called













