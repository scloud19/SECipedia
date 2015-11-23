General Tips
	mongod --fork
		forks the process so that it runs in the background
	tab autocompletion in the shell
		
Wire protocol
	Determines now mongo servers communicate with eachother, or the applications


Journal
	"You write about what you're going to do, and then you do it"
		AKA Write-ahead log
	Ensures consistency in case of some failure

General Info
	Missing fields will be evaluated as null in mongo
		If you index a field that has a few values missing, these will be evaluated as null

	When sorting a field with different datatypes, Mongo has a consistent way of sorting these values


The "local" db is usually used in replication and it contains the startup log
Storage Engine
	Can now by swapped out
	Interface between DB and the hardware (memory and disk) that it's running on.
	
	Affects
		Directly affects CRUD operations
			Data structures used to store data
				These will always be BSON but it might change the way BSON is represented on disk
					Ex: compression or different ways of representing the BSON on disk
			Affects the format of indexes.
				MongoDB used Btrees for its index formating
				Wired Tiger uses B+ trees
	Affects the form of what the file system takes
	What the in-memory model of the data looks like

	What it doesn't do
		Change how you perform queries
			Not at the shell level or driver level
		Change behavior at the cluster level

	Locking
		Prior to MongoDB 3.0
			Database locking
		Currently have collection level locking
			You can work around this
				Even if your documents are in the same collection, you can shard the collection and distribute the load among multiple servers which can independently (and simultaneously) perform the writes.
					This isn't ideal, but it will work.
		Occurs to prevent data corruption when 2 processes try to access the same data on disk at the same time
			MongoDB thus has a multiple readers, single writer lock.
				You can have as many readers as you want, but they will lock out any writers.
				As soon as one writer comes in, it locks out all other readers and writers

	As of March 2015 the choices are
		MMAPv1
			Classic MongoDB storage engine
			Maps the data files directly into virtual memory which allows the operating system to do most of the work of the storage engine

			Called MMAPv1 because it makes use of the MMAP system call in Unix to map the files on disk directly into virtual memory space

			Has collection level locking
				The granulatory of the lock may/may not effect your performamce
					This depends on your set-up

			Journaling

			Indexs
				Point to an address space where the document presides

				Stores in B-trees

			Data on disk in BSON
				Bits are mapped from disk to virtual memory
		WiredTiger
			mongod --storageEngine wiredTiger
				If you do this command and have mmapv1 files in your data/db/, you will need to remove them all.
			Can be used in other DBs other than Mongo
			Created by the eponymous company (now part of the MongoDB company)

			Has document level locking (in contrast to MMapv1's collection locking)
				Technically it isn't a document lock, it's just an very good concurrency protocol.  The effect of this is document level locking

			Your writes should scale with the number of threads 

			Brings compression (which was not in mmapv1)

			B-trees
				Stores all data on disk this way
					mmapv1 utilizes this for storing its indexes, but not its data

			Unlike mmapv1, You don't have to worry about document movement or padding factor

			New writes initially get used to files in un-used regions and then are incorporated in with the rest of the data in the background later

			During an update will actually write a new version of documents, rather than overwriting existing data the way mmapv1 does

			Compression
				Snappy (default) - Fast
				zlib - more compression
				none

			Two caches
				Wired Tiger cache
					.5 of RAM (default)
				File System cache 	

				Q: How does information get from the WT cache --> FS cache --> Disk
				Side Note:  The WT cache can have a differnt format than the FS cache and FS, thus there can be compression.
				A: This occurs during a checkpoint (which is by definition the whole flow that occured in the question).
					New checkpoints occur 60s from the end of the last checkpoint.
					Each checkpoint is a snapshot of your data.
						The last checkpoint doesn't get deleted until the current checkpoint is fully written to disk
					You technically don't need to have journaling enabled, but it is recommended
			data/db/
				Under wiredTiger, you will see that it stores files in a certain way, this includes
					colleciton-*
						WiredTiger is totally indifferent to databases as a whole, it stores everything at the collection level

Index
	About finding your documents faster

/data/db
	mongod.lock
		Tells other mongod processes to stay out


Modeling Considerations
	In Mongo, files are given a "chunk" on disk that's "capped".  But, what if the file needs to grow?  The whole file would need to get moved to a seperate place on the disk.  This is very expensive, and in Mmapv1, indexs point to locations of files on disk, so all of the indexs would have to be updated as well.
		Q: How much file size should we allocate? 
		A: Mongo 3.0 uses Power of 2 sized allocations.
			We get this when there's padding on the collection, i.e. NO
				db.createCollection("foo", {noPadding:true})

			If you initial document is under 32 bytes, it will allocate 32 bytes
			Over 32 bytes = 64 allocation
			Over 64 bytes = 128 allocation

			This process will go all the way to 2 MB, and at that point it will just add 2 MB at every step

			The power of 2 sized allocations works very well for document switching (say one document had to move), now there's a standardized hole for the other documents to go into.  In other words, documents that grow at a constant rate will move less often as time goes on.

		Q: What if we know in the beginning how large our document is going to be?
		A: You can prefil your document, maybe with an extra string field that you don't need.  Then, you can immediately unset that field, and the extra space becomes padding.
			Now your document doesn't move file locations as it grows

db.foo.stats()
	extents
		Are address spaces (in memory) where we can put documents.

In Mongo, we map

Indexes
	Queries will not use partially-built indexes: the index will only be usable once the index build is complete.
		This is something to reflect on for background builds of indexes
	Any operation that requires a read or write lock on all databases (e.g. listDatabases) will wait for the foreground index build to complete
	In the document, the indexes are built from bottom-up
	The _id index is automatically created by Mongo
		_id is immutable
	You can index the contents of an array
		Called "multikeys"
		There would be a unique entry in the B-tree for each element in the array 
	Indexes are unique at the collection level
	Fieldnames are not in the index

	You can create an index for a field that is composed of different data types

	Can index subdocuments and subfields
		subdocument index
			This is really a macro index where you are indexing everything as a unit
				Usually you are better off making compound indexes for more granular searches
	Q: Why do we have an index?
		If we don't, what happens is a table scan, where the db goes through each of the items in the whole table (SQL terms)
	'Duplicate keys' are allowed for indexes, with the exception of the ID field
		Ex: People aged 21.
			In the index, there would be references to each of the documents where age = 21

db.foo.createIndex(<keys>, options)
	options is an object
		unique: true - Unique index constraint
		sparse: true - Creates a sparse index
db.foo.createIndex({ a : 1, b: 1})
	Can index multiple fields at once, much like a sort, order of index declaration is important
	Called a compound index (more than one index)
	We could also do
		db.foo.createIndex({ a : -1, b: -1})
		But not { a : -1, b: 1}
	You can also tag an index with a name, which you can then use in the drop command.
	Mongo will create the index even if the field doesn't exist on the collection
	Pass unique: true to make sure that the index field is unique (if needed)

db.foo.getIndexes()

db.foo.dropIndex({ a : 1 })
	Use order specified when creating

db.doo.dropIndexes()

Indexes are not automatically used if they're present.
	This can be true for sparse indexes
	If not a sparse index, the index (if present) is automatically used

Sparse Index
	Lets say we have a document that has one field that is used relatively rarily.  If we index the field, mongo will create a index key for every document in the database (even those that don't have the field).
		In this case, we can utilize a sparse index to optimize this processes 
		Q: But what if the flag is in 95% of the documents?
			A: It wouldn't make sense to use a sparse index because it puts certain constraints on the query optimizer

TTL Index
	Ex: db.eventlog.createIndex({ "lastModifiedDate": 1 }, { expireAfterSeconds: 3600 })
		So 3600 seconds after the lastModified date, the document will expire
	Time To Live Index (i.e. expireAfterSeconds)
	Allows a document to be automatically deleted
		Good cases: event data, logs, session information
	If the document gets updated, the expireAfterSeconds will be reset
	A background thread on mongod deletes the documents.  This thread is run every 60 seconds, so an exact delete at 3600 seconds (for example) isn't garunteed

Geospatial Indexs
	
	Ex: db.places.createIndex({ loc : "2dsphere" })
		loc is the field with the coordinates
		Use 2dsphere (not 2d) for coordinates on the earth
	As of Mongo version 2.6, only 2d queries are supported
		For most usecases this works fine

	Ex geospatial query:
		db.places.find ({ loc :
			{ $near : { $geometry: 
				{ type: "Point", coordinates : [ 2, 2.01]}, <-- GEOJSON object
				spherical: true
			}
		 } })

		 The result is ordered by distance (closest first).  You can also specify a modifier that is maxdistance, limit, etc.

	Ex: Polygon query
		"Give me all locations in a polygon"
		"loc": {
			"$geometry" : GEO_JSON_OBJ with a type: Polygon
		}

Text search with a text-index
	Ex: db.sentences.createIndex({ words : "text"}, default_language: "english")
	words = a field with sentenses in the db
	"text" is a type of index
	 You could search the text in fields via regex, but this in convoluted and non-performant because it isn't indexed for these situations

	 There will be one index entry for every word in a sentense

	 Ex query:
	 	db.sentences.find({ $text : { $search : "cats"} } )
	 		This is case in-sensitive. and will even return "cat" 
	 		If multiple words are present, will return sentences where one of the words is there
	 	
	 	db.sentences.find({ $text : { $search : "cats dogs"} }, {score: {$meta: "text score"}, _id: 0} ).sort({ score: { $meta : "textScore"} })
	 		Score (and sort on the score) the results and suppress the _id field


	 		Will return sentences 

Brackground index creation
	db.collection.createIndex({ a: 1}, { background: true})
	Creates index in the background on the primary in our replica set
		We can still read/write to the collection while this is going on

	If MongoDB is building an index in the background, you cannot perform other administrative operations involving that collection, including running repairDatabase, dropping the collection (i.e. db.collection.drop()), and running compact. These operations will return an error during background index builds.
	
	Slower than foreground builds
		If you have a VERY large index whos collection is larger than RAM, choose the foreground option
	This is useful if all of your reads/writes occur in the primary in the replica set

	B-Tree's
		All of the buckets are greater than/equal to 50% full, except for the head bucket (which is the head of the tree)
	The foreground index builds pack these buckets a little bit "tighter"

Explain method
	"an index for every query" and a "query for every index"
		The second portion means, use explain to see that you don't have indexes that aren't be utilized.
	By default, all of the explain queries are in queryPlanner mode
		There's also an executionStats mode and a allPlansExecution mode
			ExecutionStats
				Just gives the stats on the winningPlan that was utilized in the query.
				Ex: exp = db.collectionName.explain("executionStats")

				Includes queryPlanner
				More detailed information
					time to execute the query
					number of documents returned
					documents examined
						documents examined doesn't have to equal the number of documents returned
			 "totalKeysExamined" is the number of index keys examined

			 allPlansExecution mode
				Ex: exp = db.collectionName.explain("allPlansExecution")
			 	Just like ExecutionStats but includes statistics on all available plans that you might use (not just the winningPlan).
			 	Use this solution inorder to "race" all given indexes against eachother
	See which indexes are used in a query (and returns an explain object)
	You can then run these methods on the explain object
		aggregate
		find
		count
		remove
		update
		group

		For operations with explain are write/remove/update operations
			These operations will not adulterate the data in the DB, they just similulate different situations

			These do allow us to estimate the time that different operations take and the indexes that they are using
		ex: db.example.explain().find( { a : 17} ).sort({ b : -1 })
			Look under the "WinningPlan"
				This is the plan that won, in contrast to the rejectedPlans
				Stage: IXSCAN - An idex is being utilized
					The index's used can be seen in the keyPattern or the indexName 
					If Stage: COLLSCAN, then an index isn't be utilized
						If the query is used in PROD, this is not good
				direction - How we are sorting
					In this example, we will see direction: "backward" which correlates to the -1 sort
	Overall Ex:
		Ex: exp = db.collectionName.explain("executionStats")
		exp.find({ a : 17, b: 55})
			This example assumes that we have only b indexed.

		In the "executionStats" object:
			In the "executationStages" object:
				In "inputstage" object
					"stage" : "IXSCAN",
					"keyPattern" : { "b": 1}
						we can see that there's a stage of "IXSCAN" occurring and it utilizes the "keyPattern" b = 1.  Which is the our index.
 
 		Then..
	 		"executionStages" object
	 			"stage": "FETCH",
	 			"filter" : { "a" : { "$eq": 17}}
				
				We look at the "executionStages" object and see a stage of "Fetch", which shows that we had to filter our documents where a = 17.  We can see the inefficiency of the search at this stage as we're not leveraging an index in the FETCH stage.

Covered Queries
	No need to touch documents (just uses index)
		In other words
			in the "executionStats" document where there's a .explain("executionStats")
				"totalDocsExamined" will be 0

Reads vs. writes with indexes
	Generally
		More indexes -> faster reads
		More indexes -> slower writes
		Faster to build an index post import than pre-import

currentOp() and killOp()
	Can see/kill what operations are running on a mongod or mongos

	db.currentOp()
	db.killOp(<opID>)
		<opID> is retrieved from the .currentOp()