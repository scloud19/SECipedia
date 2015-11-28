// These are my notes while going through the Mongoose docs





npm install mongoose


var mongoose = require('mongoose');
mongoose.connect('mongodb://ADDRESS/DB_NAME');
// We start a connection, but we need to know if it's successful

var db = mongoose.connection;
db.on('error', console.error.bind(console, 'connection error:'));

db.once('open', function (callback) {
	var personSchema = mongoose.Schema({
	    name: String
	});

	// Some people have a crazy laughes (aka me), so lets add this feature into our docs.
	// We can only add these methods to the schema BEFORE model compilation.

	personSchema.methods.laugh = function () {
		var name = this.name;
		console.log(name + ' has a crazy laugh! Muhahahaha!')
	}

	
	var Person = mongoose.model('Person', personSchema);
	// Construct model from schema

	// Model is a class in which we construct our documents
	// Each document will be a zach with whatever was defined in our schema

	var zach = new Person({ name: 'Zach' });
	// create a zach document

	console.log(zach.name); // 'Zach'

	zach.laugh()
	// These methods get prototyped onto Model and are exposed on each document instance

	zach.save(function (err, zach) {
	  if (err) return console.error(err);
	  
	  // Logic if successfully saved
	});

	// We can access all of our documents that are connected to the Person model (which is current the 'zach' document)

	Person.find(function (err, people) {
	  if (err) return console.error(err);
	  
	  console.log(people);
	})

	// If we don't want to output everything, we can

	Person.find({ name: /^Zach/ }, callback);

	// callback gets passed an array of the documents that are found















});

