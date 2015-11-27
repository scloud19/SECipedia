General Notes
	Node doesn't check whether Content-Length === the length of the body


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

				function (response, socket, head) { }

				EX:
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