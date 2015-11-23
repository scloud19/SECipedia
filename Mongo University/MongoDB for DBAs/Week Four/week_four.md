(P)rimary
(S)econdary
CA - Counter Argument

ToDO:
	Go back and redo all homework, also take notes for a given HW in these files (I haven't done that)

Useful commands
	mongod --logappend --fork
		if you restart your mongod process, it will keep the logs and append to them
		--fork allows the mongod to go into background

	ps -Aef | grep mongod
		Find the PID and you can optionally kill it
			This is a perfectly legit way to stop mongods
			Although DONT use kill -9
	If a replica set member goes down, your terminal will simply adjust on the next command issued
		Ex: So if you were a secondary with a prompt of abc:SECONDARY and hit enter, it should go to abc:PRIMARY
			This assumes failover occured
Misc Information
	When archecting, think about the queries.  Do we have 100 reads for every write?
		Go ahead and optimize for that usecase
	Writes always go to the primary in Mongo
	db/data directory
		mongod.lock
			Used when mongod is running so we can't run 2 mongods at the same time
		journal file
			crash recovery log, redo log
	Latency will be in an issue in PROD but not QA/staging, so always account for this and look at the usecase
Replication
	Good commands:
		db.isMaster()
		rs.help()

		rs.freeze(secs)
			Makes a node ineligible to become primary for the time specified
			If you're upgrading a mongod, put this option while the upgrade is occuring so that you don't failover to that server
	Primarily do this for
		"HA" - High Availability aka Failover
		Data safety - Backups

	"statement based"
		The mongo statement (aka logic) is sent over the wire to be executed on the secondaries.
			PRO: allows you to do compaction or repair on a secondary even though the data is not compacted on the primary.  In other words, the DB's don't have to be "lockstepped" with eachother

		contrast this with binary replication
			sending raw data across the wire
			PRO: All of the "lookup" of the data has already been done
			CON: Since you are sending very specific information, the Mongods can't really deviate on the version number (there might be different implementations of handling files across mongod versions)
				In this case, you couldn't do a "rolling upgrade" in which you upgrade one DB at a time while they're still live

	Mongo is essentially statement based, but it does have some binary replication
		ex: db.foo.remove({age:30}) that deletes 100 documents
			Will do a query on the primary and delete all of the results.  
				Then, a delete statement for each specific document is sent over the wire.  (1 delete statement for each _id field)

	Replicasets have a master(aka primary) and slave(secondary) relationship 
		at a given point in time, there's only 1 primary
		the replication process is asynchronous in Mongo (in the wild)

		A replica set is a replication cluster, it is not an entire Mongo cluster because that would include shards (where each shard includes a replica set)
		
		Replication Factor (RF) is the number of members in the replica set
			This includes all primaries and secondaries

		we can create 'write concern' where we stipulate that the write must be persisted on x mongods before a write agnowledgement has been sent to the client

		The minimum requirements for a replica set are: A primary, secondary, and an arbiter

		Give failover (if the primary goes down, a secondary can take its place).  Some databases just have master/slave replication, but there isn't an failover mechanism built in
			This occurs because all members in the set monitor eachother, so each will know if another goes down. 
				If the primary goes down, there needs to be a "consensus" that the node is really down.
					Ex: What happens if the primary is actually alive, but a secondary thinks that it's done because of some network partition (i.e. the secondary simply can't see the primary)
						So, we really need to figure out who is up and who is not.  This is where we use consensus (and elections) to figure out who should be primary and also we ulitlize elections to figure out who is alive in general
			Lets say that we have a 3 member replica set and the primary goes down.
				As 2/3 are still alive, there's a potential consensus because 2/3 is a majority.  So the 2 members are allowed to make decisions based on that.
				These 2 members will then elect a new primary.  Being a primary usually goes to the node that is the most caught up (baring any other configurations for this set).
					At this point theres 1 primary and 1 secondary.  The client (using the Mongo driver) will now start talking to the new primary.  Thus there is now a failover (there's a new primary)
						The client is "replicaset aware" and if the original primary goes down, the client will query all members to see who is the new primary.
				The failover process is not instantaneous, this can be 10s of seconds for this to occur
		DB Reads can come from a primary or a secondary while writes have to come from a primary
			ReadPreference: You can specify where you want to do reads from (primaries or secondaries)


		Give automatic node recovery
			Ex:
				We have a primary (n1) and a secondary (n2).  N1 gets writes w1 that aren't replicated yet to N2 (n1 unexpectantly went down).  In this case, lets just say that n2 then became the new primary and started recieving writes.  What happens to w1 when n1 comes back online?  In this case, that information is "rolledback" and archived.  At that point, it can recieve the stream from n2 to get it back in sync	
					If you are hesitant about having the data stored in an archived capacity, you can make it unlikely that you'll ever have to utilize the archive by getting acknowledgement of a cluster-wide commit (if this occurs, then the write in question will never be rolled back)

	Naming Conventions
		If you have a shard with the name "ZachsShard"
			Name your replica set members "ZachsShard1", "ZachsShard2", etc.
		mongod --replSet REPL_SET_GROUP_NAME
			

	Durability aka redundancy
		Occurs in replicaset (REPLICation)
			MongoDB cannot guarantee strict consistency for read operations from secondary machines
				NOTE: UPDATE this anywhere it hints otherwise

			Allows for querying to be spread out on different servers (i.e. Secondaries or primaries)
				Lets say we have a RS with P and S.  If we insert a collection FOO on P and want the S to read foo, we have to explicitly set a setting to say that we're okay with eventually consistent read semantics.  This is because S might be behind P.
					rs.slaveOk()
						We do this on the mongod secondary that we're doing the querying
				
				
				"read preference"
					We can utilize a read preference to specify where we want to perform our reads.

					Mongos can understand this and allocate resources accordingly
					
					Read Preference options

						With the exception of "primary", all of the other options could include stale data
							"perferred/best options" by Mongo U
								primary
								secondary
								nearest
						primary
							Default
							Immediately consistent reads
								No surprises with stale data
								Use this if you're in doubt
						primary preferred
							Try to query the primary, but if you can't find the primary, take the read from the secondary
							CA: "If I can tolerate reading from a secondary when the primary is down, then why cant I tolerate doing that all of the time?"  Why not switch to secondary?
						secondary
							Read only from secondary
								This keeps the load off of the primary
							Use this for certain reporting workloads
								This are expensive queries that are adhoc
									EX: Data wharehouse queries
						secondary preferred
							Use the secondaries first, but use the primary if you can't reach any secondaries
								CA: In the case where the secondaries are down (which is rare), we are going to dump a large load on the P.  Can it handle this load and has this been tested?
									If you have lots of capacity, then secondary preferred is fine
						nearest
							Go to the nearest member of the set in terms of network latency
							If your cluster is spread across datacenters, use this option or just read loads in general
							You can also tags to select which servers in a cluster you'd like to talk to
							Of all of the options, nearest will spread the reads the most evenly (this isn't a perfect balancing)
				This can minize geographic latency if you have a servers spread over availability zones in AWS
				You can also spread out workloads over different secondaries
				This allows availability
					If P goes down, we can pull reads from Ss
				Remember, if you are pulling reads from secondaries it can potentially be from stale data (secondary not updated yet from primary)
					ALWAYS assume this and ask yourself "Is this use case okay?"

	Availability

	Initiating a replica set
		2 different methods
			Start replica mongods
				sudo mongod --port 27019  --dbpath /data/d3 --fork --logpath /data/d3/mongodb.log --replSet test

				You'd want to do this for every mongod in the RS


			Use mongo shell to go into primary
				mongo localhost:27017 (for example)
			rs.initiate()
			rs.status()
				members.name
					Copy this host identifier (without the port obviously)
						This is very important, if you don't use the similar host identifier, there will be errors
			rs.add("PASTE_HOST_IDENTIFIER:PORT")

		OR you can do the procedure below

		rs.help()

		This occurs on 1 member of the replica set 
		1) specify the config for the set
			Ex:
			cfg = {
				_id: "abc", <-- replica set name
				members: [
					{ _id: 0, host: "NON_IP_ADDRESS:PORT"},
					{ _id: 1, host: "NON_IP_ADDRESS_1:PORT"}
				]
			}
				This config gets placed in a collection called system.replset
					Is a singleton collection.  If changing the configs, don't change this collection manually
				Best practices
					You can use IP addresses, but it isn't best practice.  What if the machine needs to be changed, etc.
						Thus use DNS for these A records and pick an appropriate TTL
							If a machine dies, we need a relatively shorter TTL because we need replace the machine and get it online (assuming this machine has a new IP address).  The TTL could be something like 5 minutes (probably take us this long just to get the new machine up and running)

					Don't use localhost (or anything in /etc/hosts)because that is different across different machines

		2) initial data
			If you have been using a mongod for a while without the replset option, you can use this option to hydrate all of the other members of the set with data.  Only one member can have the initial data, all other members have to start off empty.  The empty members will then sync from the initial data.
				Takeaway: Initiate on the replica set that has the initial data (if this data is needed)

		To officially initiate the set
			rs.initiate(cfg)
				This should take about a minute to start

		In the data directory of the mongod, you will see files prefixed with "local"
			These are "system files" for the particular mongod and will not be replicated in the RS
				This database will contain the oplog
					Statements used for replication onto other memebers



