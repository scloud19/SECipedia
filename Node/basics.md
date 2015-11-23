Tips. 
	We think of Node as a web server, but it can be a file server, email server, etc.
Basic Definitions
	Protocol
		A set of rules two sides agree on to use when communicating
		TCP (Transmission Control Protocol)
			The protocol that defines how information is sent/recieved
				This can then encapsulate other protocols (HTTP, FTP, etc)
					TCP splits this information into chunks and sends it through the socket.  Each of these chunks is called a packet
						In traditional architecture, the socket is opened and closed with every request/response
							"Web sockets", on the other hand, allow us to keep a socket open

						This TCP connection (with its chunks) is what Node see's as a stream.

		IP
			The protocol for identifying devices
		TCP/IP
			We refer to these together because the transmission protocol needs a protocol to identify where it's going to (IP)
	Mime type:
		A standard for specifying the type of data being sent
			If a browser sees a MIME type of 'image/jpeg', it knows it needs to render an image, etc.
	Socket
		The a line where information flows between 2 devices
	Port
		Once a computer receives a packet, how it knows what program to send it to

		IP_ADDRESS:PORT
			AKA Socket Address  
	Stream
		A sequence of data made available over time
			Pieves of data what eventually combine into a whole

			Data is split into chunks and then goes through the stream

			Where to stream to:
				Files, internet endpoints, anything that can accept a stream.
					You can also make compressed files (gzip aka *.gz)
						ex:
							var zlib = require('zlib');


		Types of streams
			Readable - You can only read from the stream
			Writable - You can only write from the stream
			Duplex - You can do both
			Transform - Allows you to change the data as it moves through the stream (you can to reads and writes)




			When you think of a type of stream, you think of it from Node's perspective. This is what defines a stream as readable/writable
				Ex: Request/Responce cycle with the browser and web server (with Node). 
					When the browser makes a request, from Node's perspective, it is a readable stream.  By similar logic, Node's response is a writable stream.

			In general, Stream is an abstract class that we inherit from but we never implement

			Creating a read stream
				var fs = require('fs');
				var readable = fs.createReadStream(__dirname + '/greet.txt', {encoding: 'utf8'});

					You can also change the size of the buffer through the highWaterMark key/value pair in the object parameter (above)

					// This starts placing contents of the file in the buffer.  Everytime the buffer gets full, the 'data' event is emitted with the buffered data.  If the data size is less than the buffer, than one data event is emitted.  This process keeps going untill there is no more data left to stream. 
				
					Without 'utf8' being passed, you will get the pure buffer output.  However, with utf8, you will see the string.

				readable.on('data', function(chunk){
					console.log(chunk);
					})

			Creating a writable stream
				var fs = require('fs');
				var writable = fs.createWriteStream(__dirname + '/greetcopy.txt');

				// So if we had a readable stream going on (like above), we could persist the data

				readable.on('data', function(chunk) {
					writable.write(chunk)
					})

					Just getting the data event starts the "reading" of the stream
			Pipes
				Connecting two streams by writing to one stream what is being read from another.
					In Node, you pipe from a Readable stream to a Writable stream.  This is done on a chunk by chunk basis

					If the stream that you're piping too is also READABLE, then you can pipe to another stream, etc.

				Ex:
					readable.pipe(writable);
						pipe() returns the destination of the pipe.  So if writable was duplex we could then read from it and pipe it somewhere else.
							readable.pipe(writable).pipe;

			Transform stream example (via zlib library)
				Remember, these streams are readable and writable

				When using the zlib library, every time a chunk is sent to it, that chunk is compressed.  Since the stream is readable and writable, you can pipe it to something else.

				Continuing from the long running example.
					
					var zlib = require('zlib');

					var compressed = fs.createWriteStream(__dirname + '/greet.txt.gz')

					var gzip = zlib.createGzip();

					readable.pipe(gzip).pipe(compressed);
						We're taking the readable stream, piping it into gzip, which creates a readable/writeable stream, which we can pipe into the file stream.
							Remember, this whole "pipeline" of actions occurs one chunk at a time.

						Remember, compressed (above) could be writing to a database, or an internet connection, etc.
							 As long as the recieving OUTSIDE stream is implemented correctly, i.e. it does reads or writes a chunk at a time, we can stream to anywhere.





	Buffer
		A temporary holding spot for data being moved from one plave to another
			A buffer is stored on the HEAP in V8
				So if you are loading large files into the buffer, ex: fs.ReadFile and many users are doing this (thus many buffers) you can get into a big problem with your app using a lot of memory
		Intentionally limited in size

		Usually, a stream fills a buffer, and at a certain time, the data in the buffer is computed/processed as a "chunk"
			Ex.  When you see "buffering" when streaming videos

		var buf = new Buffer('Hello', 'utf8')
			This initializes the buffer to 5 characters only
				so if we
					buf.write('wo')
						then the result of buf.toString() === 'wollo'
		console.log(buf);
			buf is binary, but it will be shown in the console as HEX
		console.log(buf.toString());
			Will let us convert back
		console.log(buf.toJSON());
			Will get us a object with a data array, (with each index representing a base10 UT-8 number, which corresponds to character in the string)
				Ex: { type: 'Buffer', data: [72, 101, 108, 108, 111]}
		In general, you can use the buffer like an array
			buf[2] === 108

	File System
		ex: (Lets say this code is in the test.js file)
			var fs = require('fs');
			var greet = fs.readFile(__dirname + '/greet.txt', function(err, data){
				console.log(data)
					//This will output a buffer in HEX format, if we want to get string text back, simply pass in the encoding 'utf8' as the last parameter to readFIleSync


				})
					Due to the callback, in Node this code will run asyncronously and V8 will continue executing JS.  However, if there wasn't a callback (just the assignment to greet).  This would be blocking and synchronous.

				__dirname : Utilize this if greet.txt is in the same folder as test.js
				__dirname gives you the path to the current directory

	Character Set
		A representation of characters as numbers
			Each charavter gets a number.  Unicode and ASCII are character sets
		Each unique character set has different number associations to different letters
			The larger the character set, the more numbers involved, which means we need more bits

	Character Encoding
		Ex: UTF-8
		How characters are stored in binary
			The numbers (or code points) of the character set, are converted and stored in binary.
		Yes, you can only store numbers in binary 1 way, but the character encoding says how many bits each character number can use
			Ex: UTF-8, 8 bits represent each character number
				This occurs even if it doesn't need all of them

			More bits, the larger the character set that we can use
				UTF-8 is the most popular for the internet
Tips
	store non-secure configs inside of .json file and use require(./configs.json)

	Watch how many files (or any type of thing loaded into the buffer).  Every time this occurs for a user, this takes up memory.  So large buffers times many users can crash the program.

	Use callbacks (promises) whenever possible.  If you aren't doing this, you are writing blocking code.  Also utilize streams wherever possible

	If setting module.exports to an object, utilize an es6 class to instantiate that object and then set it to module.exports

	Always utilize 'use strict';
		This will also allow you to utilize ES6 classes in certain versions of node

	You can set a class equal to module.exports.  Thus you can utilize them in NPM modules
		Ex:
			module.exports = class Greetr....

			Then in file that you're inporting into
			
			var Greetr = require('./JSFILE')
			var greeter1 = new Greetr()

Node Basics
	Javascript is synchronous while NodeJS is asynchronous
		So the Node "wrapper" of the V8 Engine is asynchronous.
			But v8 would be synchronous.

	node
		can be used by itself in CLI to start an interpreter

	Events In Node
		System Events
			C++ Core
				libuv
					Implemented in Node
					Manages events coming from the operating system
						This occurs through the event loop

						Basically libuv makes a request to the operating system to do something (download something, open a file, etc.)
							Once to OS is finished, it fires an event which is put into libuv's queue.

						Inside of libuv, there's a queue of items that have completed.  While libuv is running, V8 can be runnings its code
							This queue is constantly being checked by the "event loop" to see if things have finished.  During an event loop cycle, libuv might see that multiple "done" messages are in the queue.
								Then, for every done message, a callback is invoked in v8.  Only 1 callback can be invoked at a time (synchronous)
			Because different processes can be occuring in libuv and V8 at the same time, we think of node as asynchronous

			When you see "Event Driven Non-Blocking I/O in V8 Javascript"
				1)I/O are the operations at the system level
				2)Event Driven - We ask OS to do something, and when it's finished, we get a notification/event that it's done

				Because your JS can run while the above is occuring, we call overall NodeJS "non-blocking"

				Writing async code is very difficult
					Node allows us to write synchronous Javascript code while responding to asynchronous events

		Custom Events
			Javascript Core
				Event Emitter


	Modules
		Utilizes CommonJS standard


		require('./blah')
			Will execute the code inside blah.js (it automatically looks for .js files), which is inside the same directory as the MAINFILE.js i.e. "node MAINFILE.js"

			If it can't find a blah.js, it will look for a blah folder and then a blah/index.js
				Inside of the blah folder, we could have multiple files, each of which we can do a module.exports. Then, inside of index.js
				we could require any file we desired, and then do an module.exports
					Ex index.js
						var eng = require('./english')
						var spanish = require('./spanish')

						module.exports = {
							eng:eng,
							spanish: spanish
						}
			You can also require a .json file
				Can store NON-secure configs inside of it
				You would have a file with an object literal in it.  Make keys strings.

				When you require this, the variable you set it to will have the object

			if you do 2 of the same requires
				Ex: var hello = require('./hello')
				Ex: var hello1 = require('./hello')

				Both of these 2 variables will be accessing the same object.  So be careful
					This is because once you require a file, it is cached and it won't run the code associated with module.exports again.

					This this holds if you're accessing require('./hello') across different javascript files 

					If you don't want this to occur, instead of assigning an object to module.exports, you can assign a contructor, etc. that creates objects

			Requiring Node's CORE modules
				A list of these is seen at nodejs.org/api

				Some of these are loaded by default and are global
					ex: console.log

				Some aren't global
					Ex: Utilities
						var util = require('util')
							For native modules, you don't need ./
							For custom modules, simply add ./
 				Helpful CORE modules
 					var util = require('util');
 					var name = 'Tony';

 					var greeting = util.format('Hello, %s', name);
 					util.log(greeting) <-- adds timestamp to traditional console.log

	    Module Patterns
	    	module.exports is an empty object, so you can easily add to it
	    		In module file hello.js
		    		module.exports.hello = function...
		    	Then, in main file
		    		var hello = require('./hello').hello;

		    	Could also create a es6 class and then instantiate an object from that and set it to module.exports

		    	Exposing only public information, and keeping other variables private.
		    		Called the revealing module pattern
		    		
		    		var greeting = 'hey';

		    		function yo() {
		    			console.log(greeting);
		    		}

		    		module.exports = {
		    			yo: yo
		    		}

		Shouldn't accidently affect other code in the application.
		So if we created variables in blah.js (seen below), we couldn't invoke them in the MAINFILE.js
			If you want to make something available to in MAINFILE.js
				module.exports is a special object that you can attach things to for exporting.
					module.exports = {
						a: "hello"
					}

				To get this in MAINFILE.js
					var test = require('blah.js')
						When requireing, you can exlude the .js
		exports vs. module.exports
			They both point to the same object, but sometimes exports wont work the way it should (in certain module patterns)
				In general: use module.exports

	Event Emitter 
		You can have many listeners (all over your app) tied to one event
			Each of these will be invoked one at a time (not simultaneously)

			Ex: var Emitter = require('events')
				var emtr = new Emitter();
					this only listens and emits on emtr.  Each invokation of the constructor is unique, so if you needed to pass this into different files, you would have to pass the specific instance of emtr (but doing this is relatively rare)
		Patterns:
			specifying a .on("eventname") string is problematic because it is easy to mistype the event name without an error

				create a "config.js" file
					module.exports = {
						events: {
							GREET: 'greet',
							FILESAVED: 'filesaved',
							FILEOPENED: 'fileopened'
						}
					}
	HTTP Parser
		Is a parser for HTTP messages written in C.  It parses both requests and responses.  This is utilized inside of node

CURRENTLY AT
	lecture 59- 8:30