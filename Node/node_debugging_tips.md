	node app.js
		If we ever updated app.js we would need to restart the server.
		However, if we change any external files that were brought into node (i.e. from the fs module, etc.) we wouldn't need to restart the server.
			This is because app.js is compiled into machine code via node, and restarting would recompile.

	'errorhandler'
		an express middleware for catching errors and displaying them to the browser

		Use this only in DEV