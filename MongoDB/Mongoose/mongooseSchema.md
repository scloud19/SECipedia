These are my notes while going through the Mongoose docs and various other sources

Main Takeaways
	On the schema, you can set a date default that has a default: Date.now
		(see below)
	If using validations, make sure that you handle the error appropriately so the server wont crash if validation fails.

	Schema.Types.Mixed
		Utilized for objects with varied types in their key/val pairs
			Great for using on API


Defining the schema
	Each schema maps to a MongoDB collection and defines its "shape"

	If a field is submitted, but it isn't in the schema, Mongoose ignores it.

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
	var enumRoles = ['admin', 'user'];

	var blogSchema = new Schema({
	  title:  {
	  	type: String,
	  	required: true,
	  	trim: true
  	  },
  	  followers: [Schema.Types.ObjectId],
  	  role: {
  	  	type: String,
  	  	enum: enumRoles
  	  },
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

	Misc key/values in Schema
		enum
			Only allows the values given in the enum array
		Schema.Types.Mixed
			Any data type can be utilized here

		Schema.Types.ObjectId
			Mongo Object ID type
		trim
			If we're saving a string, it will trim the excess whitespace
		required
			forces us to have this field

			If this is utilized, make sure that the server doesn't crash when a validation fails.
				Utilize appropriate error handling
		match
			For strings, we can match a REGEX for validation purposes
		minlength
			The minimum length of a string
		maxlength
			The maximum length of a string

	Schema.add 
		Use to add additional keys later






