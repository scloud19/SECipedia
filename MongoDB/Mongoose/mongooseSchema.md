These are my notes while going through the Mongoose docs

Make Takeaways
	On the schema, you can set a date default that has a default: Date.now
		(see below)


Defining the schema
	Each schema maps to a MongoDB collection and defines its "shape"

	The permitted SchemaTypes
		String
		Number
		Date
		Buffer
		Boolean
		Mixed
		ObjectId
		Array

	Ex:
	var mongoose = require('mongoose');
	var Schema = mongoose.Schema;

	var blogSchema = new Schema({
	  title:  String,
	  author: String,
	  body:   String,
	  comments: [{ body: String, date: Date }],
	  date: { type: Date, default: Date.now },
	  hidden: Boolean,
	  meta: {
	    votes: Number,
	    favs:  Number
	  }
	});

	Schema.add 
		Use to add additional keys later






