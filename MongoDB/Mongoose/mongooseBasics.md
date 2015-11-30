// These are my notes while going through the Mongoose docs

// Use this guide as a basic "startup" script, etc.

Main Takeaways
	Document Instance Methods
		Very good way to make queries modular

		When defining a Model you can add methods that will be shared across all document instances

		Ex:
		// define a schema
		var animalSchema = new Schema({ name: String, type: String });

		// assign a function to the "methods" object of our animalSchema
		animalSchema.methods.findSimilarTypes = function (cb) {
		  return this.model('Animal').find({ type: this.type }, cb);
		}

		var Animal = mongoose.model('Animal', animalSchema);
			// For a third argument, you can optionally pass a collection name STRING

		var dog = new Animal({ type: 'dog' });

		dog.findSimilarTypes(function (err, dogs) {
		  console.log(dogs); // woof
		});





npm install mongoose


var mongoose = require('mongoose');
mongoose.connect('mongodb://ADDRESS/DB_NAME');
// We start a connection, but we need to know if it's successful
// If you don't currently have DB_NAME, it will be created for you

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
	// mongoose.model(modelName, schema):

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

.save() vs .update()
	If you want to fire Mongoose pre/post hooks, utilize the save method (not update)

	Utilize save if you're writing a whole object back to mongoDB

	update is for adding additional information to a preexisting object that ALREADY exists in Mongo
