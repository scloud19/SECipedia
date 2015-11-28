General Notes
	Node doesn't check whether Content-Length === the length of the body

	If utilizing sockets, check the 'clientError' event for error handling/logging (look below)

HTTP server/client
	require('http')

	The module never buffers entire requests/responses
		Thus, the user is able to stream data

	Very low level
		Module parses HTTP messages into headers and body but that's it
			Allows for many implementations

	http.Agent class
		Used for pooling sockets used in HTTP client requests
			Pool
				Keeps a certain number of sockets to be used by the program
					The sockets reused for different requests, so having a pool reduces the overhead of creating new sockets.  This also makes it so there's a limit on the number of sockets utilized
				Keeps a queue of requests, that will be offloaded onto sockets when the socket becomes available.

				The true maximum number of simultaneous sockets is controlled by the OS, but you can alter the options.maxSockets in node (which can't be greater than the OS number)

				There is a global pool that can be utilized, but it will not give granular control.

		new Agent([options])
			options OBJ
				keepAlive BOOL
					(DEFAULT-False)- Keep sockets around in a pool to be used by other requests in the future
				
				keepAliveMsecs INT DEF-1000
					When keepAlive is true, this setting dictates how often to send TCP KeepAlive packets over sockets that are being kept alive.
				
				maxSockets INT DEF-INFINITY
					Max number of sockets to allow per host
				
				maxFreeSockets INT DEF-256
					PREREQ: keepAlive == true
					Max number of sockets to leave open in a free state.

			The default http.globalAgent that's used by http.request has these values set to their respective defaults.
				If you want to customize, you must create your own Agent object.

				EX:
				var http = require('http');
				var keepAliveAgent = new http.Agent({ keepAlive: true });
				options.agent = keepAliveAgent;
				http.request(options, onResponseCallback);

			agent.destroy()
				Destroy any sockets that are in use by the agent

				You usually don't have to do this, but if you're using an agent with KeepAlive == true, it's best to utilize this function to explicitly shut down the agent that you're no longer using.

			agent.freeSockets OBJ
				PREREQ: keepAlive == true
				Contains arrays of sockets currently awaiting use by the Agent.

				Do NOT modify this OBJ

			agent.getName(options)
				For a set of request options, get a unique name to determine whether a connection can be reused.

				In the http agent, this returns
					host:port:localAddress
				In the https agent, this returns
					CA, cert, ciphers, and other HTTPS/TLS-specific options that determine the reusability.

			agent.maxFreeSockets INT
			agent.maxSockets INT
				Another way to set these configs (see above)

			agent.requests OBJ
				Contains queues of requests that have not yet been assigned to sockets.

				Don't modify this

			agent.sockets OBJ
				An object which contains arrays of sockets currently in use by the Agent

				Don't modify this

	http.ClientRequest
		The object that's returned from http.request()

		Is a Writable Stream and is an EventEmitter

			This represents an in-progress request whose header has already been queued.
				At this point, the header is still mutable using the header API in Node

				The actual header will be sent along with the first data chunk or when closing the connection.

			To get the response, add a listener for 'response' to the request object.
		
		events
			The request is an EventEmitter

			response
				args
					instance of http.IncomingMessage

				add a listener for this event to the request object.

				emitted when the response headers have been received

				If you add a response event handler, you MUST consume the data from the response object.  
					You can do this by:
						response.read()
							Whenever there's a readable event
						adding a 'data' handler
						calling .resume()
					
					Until the data is consumed, the 'end' event will not fire.  Additionally, until the data is read it will consume memory that can lead to errors.

			abort
				The request has been aborted by the client.

				Only emitted on the first call to abort()

			connect
				
				Emitted each time a client requests a http CONNECT method.
					CONNECT is an HTTP verb that is used for tunneling purposes.
						Ex: If we open a telnet session and DO
							CONNECT zachroof.com:443

							// The proxy will answer with 
							HTTP/1.0 200 Connection established

							This means that the proxy set up the tunnel to the host.  Now you can communicate with the host through the tunnel.


				function (response, socket, head) { }

				EX:
					//if confused on the ordering of events, look at http://stackoverflow.com/questions/25941175/calling-nodejs-request-end-method-before-setting-up-the-listeners

				var http = require('http');
				var net = require('net');
				var url = require('url');

				// Create an HTTP tunneling proxy
				var proxy = http.createServer(function (req, res) {
				  res.writeHead(200, {'Content-Type': 'text/plain'});
				  res.end('okay');
				});
				proxy.on('connect', function(req, cltSocket, head) {
				  // connect to an origin server
				  var srvUrl = url.parse('http://' + req.url);
				  var srvSocket = net.connect(srvUrl.port, srvUrl.hostname, function() {
				    cltSocket.write('HTTP/1.1 200 Connection Established\r\n' +
				                    'Proxy-agent: Node.js-Proxy\r\n' +
				                    '\r\n');
				    srvSocket.write(head);
				    srvSocket.pipe(cltSocket);
				    cltSocket.pipe(srvSocket);
				  });
				});

				// now that proxy is running
				proxy.listen(1337, '127.0.0.1', function() {

				  // make a request to a tunneling proxy
				  var options = {
				    port: 1337,
				    hostname: '127.0.0.1',
				    method: 'CONNECT',
				    path: 'www.google.com:80'
				  };

				  var req = http.request(options);
				  req.end();

				  req.on('connect', function(res, socket, head) {
				    console.log('got connected!');

				    // make a request over an HTTP tunnel
				    socket.write('GET / HTTP/1.1\r\n' +
				                 'Host: www.google.com:80\r\n' +
				                 'Connection: close\r\n' +
				                 '\r\n');
				    socket.on('data', function(chunk) {
				      console.log(chunk.toString());
				    });
				    socket.on('end', function() {
				      proxy.close();
				    });
				  });
				});

		continue
			emitted when the server sends a '100 Continue' HTTP response.

			This is an instruction that the client should send the request body.

		response
			function (response) { }

			Emitted when a response is received to this request.

			Emitted only once

			argument is an instance of http.IncomingMessage

		socket
			Emitted after a socket is assigned to this request

		upgrade
			function (response, socket, head) { }

			Emitted each time a server responds to a request with an upgrade

			EX: A client server pair that show you how to listen for the upgrade event.

			var http = require('http');

			// Create an HTTP server
			var srv = http.createServer(function (req, res) {
			  res.writeHead(200, {'Content-Type': 'text/plain'});
			  res.end('okay');
			});
			srv.on('upgrade', function(req, socket, head) {
			  socket.write('HTTP/1.1 101 Web Socket Protocol Handshake\r\n' +
			               'Upgrade: WebSocket\r\n' +
			               'Connection: Upgrade\r\n' +
			               '\r\n');

			  socket.pipe(socket); // echo back
			});

			// now that server is running
			srv.listen(1337, '127.0.0.1', function() {

			  // make a request
			  var options = {
			    port: 1337,
			    hostname: '127.0.0.1',
			    headers: {
			      'Connection': 'Upgrade',
			      'Upgrade': 'websocket'
			    }
			  };

			  var req = http.request(options);
			  req.end();

			  req.on('upgrade', function(res, socket, upgradeHead) {
			    console.log('got upgraded!');
			    socket.end();
			    process.exit(0);
			  });
			});

	request.abort()
		Marks the request as aborting.

		This will cause remaining data in the response to be dropped and the socket will be destroyed

	request.end([data][, encoding][, callback])
		Finishes sending the request

		If any parts of the body are unsent, it will flush them to the stream.

		If the request is chunked, this will send a string of terminating characters (aka blank line or '0\r\n\r\n')

	request.setNoDelay([noDelay])
		Once a socket is assigned to this request and is connected socket.setNoDelay() will be called.

	request.setSocketKeepAlive([enable][, initialDelay])#
		Once a socket is assigned to this request and is connected socket.setKeepAlive() will be called.

	request.setTimeout(timeout[, callback])#
		Once a socket is assigned to this request and is connected socket.setTimeout() will be called.

	request.write(chunk[, encoding][, callback])
		Sends a chunk of the message BODY

		By calling this many teams, a user can stream to the server.
			Suggested to use ['Transfer-Encoding', 'chunked'] header line when creating the request

		args
			chunk
				Can be a Buffer or string

			encoding
				Only use when the chunk is a string

			callback
				called once the chunk of data has been flushed to the underlying resource (aka filesystem)







		Sockets are removed from the agent's pool when the socket emits either a "close" event or a special "agentRemove" event.

			Ex: If you want to keep one HTTP request open for a long time and don't want it to stay in the pool

			http.get(options, function(res) {
			  // Do stuff
			}).on("socket", function (socket) {
			  socket.emit("agentRemove");
			});

			//Or, you could just opt out of pooling entirely using this..

			http.get({
			  hostname: 'localhost',
			  port: 80,
			  path: '/',
			  agent: false  // create a new agent just for this one request
			}, function (res) {
			  // Do stuff with response
			})



		Defaults client requests to Connection:keep-alive
			The connection to the server will be persisted until the next request (or an attempt at this is made)
			If no pending HTTP requests are waiting on a socket to become free the socket is closed
				This means Nodes pool has the benefit of keep-alive when under load but still doesn't require devs to manually close the HTTP clients using KeepAlive.
					You can opt into using HTTP KeepAlive (see below)

			Agent option object
				KeepAlive
					set to true
						The Agent will keep unused sockets in a pool for later use.
							These sockets will be marked so the Node process wont be kept running indefinitely (due to references to sockets in the event loop)
					Best practice to explicitly destroy() KeepAlive agents when they aren't in use, so that Sockets will be shut down.




		Ex:
		pool = new http.Agent(); //Your pool/agent
		http.request({hostname:'localhost', port:80, path:'/', agent:pool});
		request({url:"http://www.google.com", pool:pool });

	http.Server
		EventEmitter with the following events

		Event
			'clientError'
				function (exception, socket) { }

				If a client connection emits an 'error' event, it will be forwarded here.

				socket is the net.Socket object that the error originated from

			close
				Emitted when the server closes

			connect
				function (request, socket, head) { }

				Emitted each time the client requests a http CONNECT method

				Caveat: After this event is emitted, the request's socket will not have a data event listener, meaning you will need to bind to it in order to handle data sent to the server on that socket.

				args
					request
						args for the HTTP request

					socket
						network socket between the client and server

					head
						an instance of Buffer, the first packet of the tunneling stream (which might be empty)


			request
				function (request, response) { }

				Emitted each time there's a request

				In the case of keep-alive connections, there may be multiple requests per connection.

				args
					request
						instance of http.IncomingMessage
					response
						instance of http.ServerResponse
			upgrade
				function (request, socket, head) { }

				Emitted each time a client requests an http upgrade

				Args and caveats
					Same as 'connect' event above

		server.close([callback])
			Stops the server from accepting new connections
				Similar full implementation of net.Server.close()

		server.listen(handle[, callback])
			There's a similar implementation of this method with a UNIX socket server flavor (instead of 'handle', there's 'path')
				Unix domain sockets are a method by which processes on the same host can communicate.

				Instead of a IP address and port, there's a "path"


			handle OBJ
				can be set to either a server OR
				socket(anything with an underlying _handle member) OR
				{fd: <n>}

			This will cause the server to accept connections on a specified handle
				It is presumed that the fd/handle has already been bound to a port/domain socket

		server.listen(port[, hostname][, backlog][, callback])
			To listen to a unix socket, supply a filename instead of port and hostname.

			args
				backlog
					maximum length of the queue of pending connections
						The actual length is determined by your OS
							Ex: Linux
								tcp_max_syn_backlog AND
								somaxconn

		server.setTimeout(msecs, callback)
			Sets the timeout value for sockets and emits a 'timeout' event on the Server OBJ, passing the given socket as an argument.

			args
				msecs INT
				callback FUNCT

			By default, if a socket is inactive for 2 mins, it will be destroyed automatically.  
				If you assign a callback to the timeout event, you are then responsible for handling socket timeouts.

			returns the server instance

		server.timeout INT(ms)
			Timed inactivity before a socket is presumed to have timed out.

			Changing this value only changes NEW socket connections
		
	http.ServerResponse
		This object is created by Node at sent as the second parameter to the 'request' event

		Implements a Writable Stream interface.

		An EventEmitter with the following events

			close
				The underlying connection was terminated before response.end() was invoked OR able to flush to disk.

			finish
				Emitted when the response is sent

				The event is emitted when the last segment of the response headers and body have been handed off to the operating system.  
					This doesn't imply a successful transmission to the client

				After this event, no more events will be emitted on the response object.

			response.addTrailers(headers)
				For chunked transmissions
					Adds a header to the end of the message

				If you are utilizing a trailer, HTTP requires that you specify this in the initial header in the stream.
					You must also specify what the header key will be.

					Ex:
					response.writeHead(200, { 'Content-Type': 'text/plain',
					                          'Trailer': 'Content-MD5' });
					response.write(fileData);
					response.addTrailers({'Content-MD5': "7895bf4b8828b55ceaf47747b4bca667"});
					response.end();

			response.end([data][, encoding][, callback])
				Signals to the server that all of the response headers and body have been sent.

				This must be called on each response

			response.finished BOOL
				Set to true once response.end() executes

			Note: 'implicit headers' are when you set the headers without directly calling response.writeHead()

			response.getHeader(name)
				Returns a header STRING that has already been queued but not sent to the client.

				This can only be called before the headers are flushed.

			response.headersSent BOOL

			response.removeHeader(name)
				Removes a header that's queued for sending

			response.setHeader(name, value)
				Adds a header to the sending queue.

				Use an array of strings if you need to send muliple headers with the same name

				EX:
				response.setHeader("Set-Cookie", ["type=ninja", "language=javascript"]);

			response.setTimeout(msecs, callback)
				args
					callback
						Added as a listener for the 'timeout' event on the response object
							If no 'timeout' listener is added to the request, the response, or the server, then the corresponding socket is destroyed when it times out.

			response.statusCode
				When using implicit headers, this property controls the status code that will be sent when the headers are flushed

				Ex:
				response.statusCode = 404;

			response.statusMessage
				Used when using implicit headers

				If left undefined, then the standard message for the status code will be used

				After the response header has been sent, this property indicates the status message which was sent out

				Ex:
				response.statusMessage = 'Not found';
			











