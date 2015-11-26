General Definitions
	pool

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