Keys
	RS - Replica Set
	RF - Replication Factor
	A - Arbiter

Tips
	db.getSisterDB("config").shards.count();

Important Takeaways:
	In a sharded cluster, at a minimum, use 2 RF on each shard.
		To get consensus, you would be adding 1 A (if you didn't go with a 3 RF)

		You also need to have 3 config servers

	For automating spin-ups of architectures look at shell scripts
		Ex: Automating RS, sharding, etc.  Really anything

	Make sure to have a mongos utilize the default mongo port (if you don't set a --port it will default to 27017.)

	When creating queries, order doesn't matter.  BUT, order does matter when creating/utilizing indexes

	Only shard the big collections

	Pick your chard key carefully
		You can't change your shard key once it's set
			You would have to create a new collection and migrate your data over to it

	Adding shards is fairly easy, but each new shard is empty, so new data has to migrate over to it (it might take time/bandwidth)

	Use DNS names for server names in configs
		It would be very difficult to change
			If switching config servers, PLEASE CHECK DOCS before you do it


Sharding
	"Shards" aka partitions

	A given BSON document will only be on 1 shard at a given time
		Althoughm, it is possible for values to move for rebalancing purposes

	Within a shard, we can have a replica set

	Use RS for high availability, use shards for scale out


	Range-Based partitioning
		This is how documents are distributed in a shard

		The documents are distributed amongst shards via a shard key which matches to a given shard's SHARD KEY RANGE
			This paritioning occurs at the collection level
				So the shard key splits up a collection to be distributed amongst shards
		
			Documents in the same collection, which share the same shard key will all be on the same shard.


		Documents whose shard key is close to the other shard key in sort order will tend to be on the same shard as well

	Each shard key is matched to a shard key range for a given shard
		"Chunk"
			All of the documents that live within a certain key range on a shard
				Multiple chunks CAN live on one shard
			The default chunk size is 64MB

			As chunks grow beyond the specified chunk size, a mongos instance will attempt to split the chunk in half.  
				Spits may lead to an uneven distribution of the chunks for a collection across the shards.
					In these cases, the mongos instances will initiate a round of migrations to redistribute chunks across shards.
		
	Throughout a sharded collection, there are 2 processes that are occuring in the background
		These actions are really about getting an even distribution of data amongst the shards
			1) Split
			2) Migrate
				There's a balancer in Mongo which decides when to invoke the migrate function (and where to migrate to).
					The balancer balances on the number of chunks per shard (no other metric)
						Hopefully over time, this will become more advanced

						The goal of the balancer is to have an equal number of chunks on every shard
					Ex: If you had a sharded collection that only had 20mb of data, all of that data would just sit in one shard

		Ex: Lets assume that we have a chunk that is getting really large (it has exceeded our 64 MB chunk limit)
			To solve for this, the system will do a split operation
				This will split the key range of the chunk into two different key ranges.  
					At this point, the system needs to find a good median key to split the chunk in half.

			Although now we have the 2 chunks on the same shard, so the Migrate operation will potentially move the chunk to maintain balance within the shards (it will only do this when there's a lack of balance between the number of chunks on the different shards)
				This migration processes is expensive, we have to physically tranfer the chunk and we need to do write operations (receiving shard) and delete operations (leaving shard)
					Due to this, the system will intelligently transfer the chunks (it wont do it all at once, etc.)
						Ex: There will never be more than a single migration between a given pair of nodes
				Throughout the migration processes, the data is still live.
					Read/Write operations are still permitted aka there isn't any kind of lock occurring, etc



	The higher the number of shards, the higher the RF needed
		~6 Shards would need 2 copies of the data per shard
		~1000 shards would need 3 copies of the data per shard
			This addition is because there's a higher probability that you'll have two servers down at the same time within one shard

	Sharded cluster architecture
		There are:
			Multiple Mongod instances
				share the data of the sharded collection

			Config servers
				Small mongod instance that store the metadata about which chunk maps to which mongod and overally metadata on the cluster

				In a production level ENV, if you are utilizing sharding, you need 3 config servers (these will have identical data)
					These servers are very important, without these, everything fails

					Three config servers are typical in a MongoDB cluster with 1,000 machines


			Mongos
				The client connects to these instances

				If you connect to the mongos over the mongo shell, you get getting an aggregate view of the cluster
					In mongos from mongo shell
						use config
							You can look at the config database

				Theres no persistent state, think of these as load balancers
					On once they get information from the config servers, they will cache that in ram

						Due to this you can add/remove these servers a lot easier compared to the mongods holding the data

				Gives a view of the whole cluster as a single logical entity.
					So the client doesn't need to think about which shard maps to what mongod, etc.

				Will talk to the config servers and the mongods

				When a client asks for specific information
					Mongos will query the config servers to see where the data lives (i.e. what shard), this assumes that the mongos just booted up.  Otherwise, it could just pull these configs from RAM.
						Once it gets that information, it will route the query to the appropriate mongod.
							If it communicates with more than 1 mongod, it will merge that data before giving it back to the client  
	Setting up a shard
		Things to consider
			How many shards initially

			Replication factor

		Ex: Lets set up a shard
			Specs
			How many shards initially: 4
			Replication factor: 3
				This would give us 3 x 4 = 12 mongod 'shard servers'
					In the real world, you would have these on 12 different boxes
			4 mongos processes 
				How many mongos processes should you generally run?
					A: However many you want but usually much more than one
				a) This could also be put on the box with the node server, with will then be routed through the localhost dynamic and will be quicker at routing.

				OR you could simply put this on the mongod 'shard servers', which might be a better seperation of concerns.  This is a best pattern given security practices and overall is the best solution (although you do see pattern "a" sometimes).
					In general, you can house 1 shard server (mongod) with
						1 mongos OR
						1 config server OR
						1 arbiter
					A typical pattern is to have a mongos on every shard server, and never have 2 servers connecting to one mongos
						Setup a connection pool to mitigate this problem
			3 config servers (you ALWAYS run 3 config servers)
				For the mongos and config servers, these are very lightweight, you could always spread this out amongst the shard servers (Try to spread this out on DIFFERENT RSs if possible)
					As the first shard always has the unsharded collections on it, it might be wise not to put any additional server/load on it

			Overall process in a shell script
				Assumptions
					We are doing this on our own box, so make the port numbers unique
				create data directories for each shard and number then for every RS member
					Ex: 
						mkdir a0
						mkdir a1
						mkdir a2						
						mkdir b0
						mkdir b1
						mkdir b2
							etc...

				create data directories for each of the config servers
					Ex:
						mkdir cfg0
							etc...


				Invoking the config servers
					mongod --configsvr --dbpath cfg0 --port 26050 --fork --logpath log.cfg0 --logappend
						etc... (X3)

				Invoking the shard servers (mongod data servers)
					--shardsvr
						is a defensive flag.  People wont be able to connect to the mongod and do stuff they shouldn't do (given that this is a sharded server)
					mongod --shardsvr --replSet a --dbpath a0 --logpath log.a0 --port 27000 --fork --logappend
					
					mongod --shardsvr --replSet a --dbpath a1 --logpath log.a1 --port 27001 --fork --logappend

					mongod --shardsvr --replSet a --dbpath a2 --logpath log.a2 --port 27002 --fork --logappend

						etc... Do this for every shard, etc.

					--shardsvr: will automatically pick a port that isn't 27017, although you can manually create it too.

				Invoking the mongos processes
					You always want to route traffic through the mongos, NEVER have the client application interact with the mongods
						From a DB Admin perspective, you only want to do administrative actions through the mongos
							One exception for logging into the mongods directly is for administration of the replica set, etc.

					mongos --configdb DNSNAME:26050, DNSNAME:26051, DNSNAME:26052 --fork --logappend --logpath log.mongos0 --port 26061
						--configdb will automatically pick a port that isn't 27017, although you can manually create it too.
						We need to tell the mongos where the config servers live

						if you don't set a --port it will default to 27017.
							So, because you want all default traffic to be routed a mongos instance, leave off the --port of ATLEAST ONE mongos instance.

					If you do a cluster of the mongos', then only one of them will be the chunk rebalancer.  

				Show the processes for logging
					ps -A | grep mongo

					tail -n 1 ALL_THE_LOGS

				We need to initiate the shards
					1) Initiate the RS within the shard
						Find all shards that start with port 2700
							ps -A | grep mongod | grep 2700

							Connect to primary

							rs.status()
								this will show us that we haven't initiated the RS

							rs.initiate()
								will initiate the RS with the default config

							 rs.add("ADDRESS:PORT")
							 	for the 2 memebers

							 rs.conf()
							 	Will now show us the 3 memebers

							 rs.status()
							 	Will show an
							 		"errmsg": "sincing to: PRIMARY ADDR"
							 	We should wait for this to finish before adding the RS as a shard



					2) "add" the shard
							Connect to a mongos (you can just do the one on the default port)
								sh.addShard('a/PRIMARY_ADDRESS:PORT')
									this will automatically add in the secondaries, etc.

									a is the RS set name

							Check to see if the shard is running and that all of the memebers of the RS have been added to the shard

							Replicate this process for all of the shards

							sh.status()

					Look at the automated script for this processes
						FILE 
							our_setup_script.js
								this script assumes that everything is running on our machine

								It also stops to make sure each RS is initiated before adding a given shard.  If you were adding say 50X shards, you could instantiate the RS's in parellel and then wait for each of them to instantiate before adding.  Right now it would block the RS instantiations occuring at the same time.

						mongo --shell our_setup_script.js
							will run our script

							If you architect the script to be run HEADLESS then just run 
								mongo SCRIPT.JS
									This will return to the CLI after execution (not the mongo shell)

					To verify
						db.shards.find();



			To Shard a collection
				All of your unsharded collections are housed on shard[0] of the cluster.

				Enable sharding on each db to shard its collections
					sh.enableSharding("DB_NAME")

					To verify:
						sh.status()
							databases OBJECT
								If you see "partitioned": true that means sharding is enabled on the database
				Shard the collection
					You can have a collection with preexisting data and THEN shard
						Although, there MIGHT be a limit on the collection size
					To verify.
						sh.status()
							You can see chunk information, etc.
								Per database, you will see how many chunks reside on each shard
						You can also verify on the collection itself
							DB_NAME.COLL_NAME.stats()
								 nchunks
								 	will show you how many chunks are utilized for the collection

								You can also see metadata about what is on what shard, etc.

					sh.shardCollection("DBNAME.COLLECTION", {_id:1}, UNIQUE_BOOLEAN)
						*If your shard key (aka partition key) is the ID field
						UNIQUE - Is this field unique?
			To play the a sharded collection, always log into a mongos
				Ex on the mongos
					db.example.insert({ b : 1, c: 1, d: 1})
						Remember, if you're going to insert onto a shard, it needs to have a given shard key in the insertition statement
							Note: Whatever shard key you utilize will become an index on the collection
								The shard key is your BEST index, and the one that you should use most often.  This is because it delegates a query to a specific server/s (i.e. targeted query) If it wasn't targeted it would be called a "scatter/gather query"
									If you want to see this in action, make an explainable object to see query stats/index utilization.
										exp = db.example.explain("executionStats")
											exp.find({ KEY: VALUE, KEY: VALUE...})
									If you're utilizing a scatter/gather query, try to index that query.

									Queries don't have to utilize the entire shard key to act like a targeted query 
										Ex: Say your shard key is
											{ a: 1, b:1, c:1 }
												If your query is only utilizing
												 { a: "hey", b:"yo" }
												 can still be a targeted query.  It might not isolate your query to 1 shard (might be 2 shards), but it is a lot better than hitting every shard

					db.example.createIndex({ e: 1, f:1, a:1})
						Whenever you create an index, it will be created on each shard that houses the collection

			Shard Key Selection
				TIPS:
					If you see a field that you utilize in most of your queries, think of that for your shard key.

				Usually a field that's involved in most of your queries

				In general, shard keys with non-uniform key-distributions are okay.  The balancer will automatically split chunks and balance as it sees fit.

				Needs sufficient cardinality
					I.e. there's enough values that we can really spread the data out amongst the shards

					Make it so a shard key value can fit on 1 shard
						If you can't do this (thus the shard key isn't granular enough)
							You can consider a compound shard key

				Is the shard key monotonically increasing?
					An example if monotonic increasing is a timestamp field.
						If this is our shard key, we will always be directing our writes to the last shard.
							Yes, the mongos will eventually remap the "chunk" mapping and also move the chunks to keep everything balanced, but this isn't efficient.  And all of our writes are initially going to 1 server
						But say we have an orders collection, which has a company field and a date field, we could then create a compound shard index that would utilize this combonation.  Thus it wouldn't really have this monotonic problem
					Another example is a BSON object ID
						If you want to utilize the object id, utilize a hashed shard key

	Pre-splitting chunks in a sharded collection
		Normally you dont need to do this but sometimes it's useful

			Say we start out with an empty collection, we will basically have an "infinite" chunk with an infinite shard key range. This will be on Shard 0.

			The data will then start to be inserted, and the chunk will then split, and split, etc.
				After this splits, the chunks will need to migrate between shards.  Once the chunk has moved, the corresponding shard key range of the chunk will then define the key range of the shard. (Any further inserted documents in that corresponding key range will be DIRECTLY inserted into that shard, not shard1)

				The problem can occur when we're migrating data into Shard 0 quicker than it's moving out (bulk instert, etc).
					Now we don't have that nice initial chunk distribution (as seen above)
						In this case, we might want to pre-split our initial key range manually
							To do this:
								sh.shardCollection("DB_NAME.COLL_NAME", {SHARD_KEY: VALUE})
									This collection can be empty or have data

									sh.splitAt("DB_NAME.COLL_NAME", { SHARD_KEY: SPLIT_VAL})
										Just make sure all of the splitAts allow the chunks to be uniformly distributed
											If you had 10 shards, you could maybe do 10-20 chunks and be safe

											This doesn't have to be perfect, as long as the split points are enough to get the chunks "homed" at different shards
												Remember, the chunks can always split into the future
										Once you do the split, it doesn't distribute the chunks amongst the shards.  That is done later afterwards by the balancer.
											Thus, wait for the balancer to distribute the chunks before doing the bulk load.
					db.chunks.find()








