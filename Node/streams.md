? - Double Check
These are the notes that I took while reading/taking:
Node Streaming Handbook

TODO:
	Blend this with stream info in basics.md

Streams
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

		Readable Streams
			Produce streams that can be fed into a writable, transform, or duplex stream

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









