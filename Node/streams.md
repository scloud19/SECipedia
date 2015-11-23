? - Double Check
These are the notes that I took while reading/taking:
Node Streaming Handbook

Streams
	.pipe() is the primary composition operator.
		this method also has a backpressure mechanism which allows us to throttle writes for slow consumers.

		Think of piping different streams together to make coding more elegant and modular

		All streams utilize .pipe() to "pipe" inputs to outputs


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



