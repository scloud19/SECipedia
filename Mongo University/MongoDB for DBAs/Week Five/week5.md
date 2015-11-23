(P)rimary
(S)econdary
(A)rbiter
(M)ember
1P/1S - 1 primary and 1 secondary
DC - Data center
Replica Sets (rs)

Tips and LARGE takeaways:
	When setting your Mongo servers, it is very important to set a max connection pool on each server.

	When doing write concerns, make sure to set a timeout, if this doesn't occur, the write will block infinitely.

	Normally we connect to a mongod from the command line with "mongo"
		We can do subconnects to other dbs within the shell as well
			var server2 = new Mongo('localhost:27002')
			var server2_test = server2.getDB('test')

			or
			var server2Test = new Mongo('localhost:27002/test')
			server2Test.foo.count()
				wherever db was in the normal shell, you can use 
				server2Test

			We can setSlave okay on a subconnection
				server2Test.getMongo().setSlaveOk()

	Every conneciton in Mongo requires 1 MB of RAM on the server

	Make sure to save DB snapshots every so often for backup purposes.
	Lets say that we did a snapshot every 24 hours, that would mean that
	our oplog size would need atleast 24 hours of "space" so we could
	have a point-in-time restore. 

	Sense the output of functions run in the shell yeilds JSON you can return
	specific values
		ex: db.isMaster().me
			Gets what server the db is on

If there are two S's that get segmented from the P through a network partition, if the two S's can still communicate with eachother (and one has more fresh data than the other) then the fresh data will hydrate the secondary's data

Limits on Rs's
	Has to be less than/equal to
		12 members
		7 voters


Rollback
	Reverts write operations on a former primary when the member rejoins its RS after a failover.  This is necessary only if the primary had accepted write operations that the secondaries had not replicated before the primary stepped down.  When the P rejoins as a S, it reverts, or "rolls back" its write operations to maintain DB consistency with other members.
		If this does occur, you must decide whether to apply or ignore the rollback data.
		MongoDB writes the rollback data to BSON files in the rollback/ folder under the db's dbPath directory.
			You must manually reapply this data.

		To prevent rollbacks of data that have been acknowledged to the client, use "w: majority" (see more under "Cluster Wide Commit" below)

Questions:
	In a 2 node RS, one will initially become primary.  Given the election process
	and majority, how is this possible?

	Config options

	To change a config (this will make live changes as well) and put a slave delay
	on the second memeber
		var cfg = rs.conf()
		cfg.members[2].slaveDelay = 120
		rs.reconfig( cfg )
			For a reconfigure to be successfully applied, there must be majority consensus
	Config object
		members array
			object/s
				arbiterOnly: true
					Means that there's an arbiter
				priority: <N>
					If two members can be primary, the one with the higher priority will be the one that wins.  By default, the number is 1.  0 indicates that this RS member will never be primary.
					You can have decimal priorities as well (0.5)
						<N> can never be negative

					In certain situations it is not uncommon to set up a manual failover
					in the settings
						EX: 3M RS with 1P/1S in one DC and 1S (AKA N1) in another DC.  If we never want N1 to become primary (or only invoke a manual failover), we simply give it a priority of 0.  If things are starting to get really bad in the first DC, we can set the priority of N1 to be 2 and it will become the P (in this situation).
				hidden : bool (FALSE is default)
					Will disallow queries to get automatically routed to the mongod (you can still directly connect to it)
						Maybe the server is smaller, too much latency, etc. 
					If a disaster is occuring, we can always take off the TRUE identifier.
				votes:
					A replica set member can only have 0 or 1 vote.
					It is best practice to leave this as the default setting (dont change it).
						Exception
							Mongo only allows seven voting memebers in a RS.  So if you have more, you need to set their vote to 0
				slaveDelay : <N of seconds to delay>
					Additionally, set the priority: 0, hidden:true 
						hidden: true
							You dont want to be actively reading stale data

					A member must lag, it can never be fresher than the delay time

					A rolling backup, good for situations where someone accidentially deleted something and you want to get it back quickly (and you don't want to do a full restore of a backup which can be older).  This can also be good incase you just launched a release and want to quickly roll back if need be.
						Helpful for getting a view of the DB between backups
						Helpful during development when using experiemental queries
						The N can be something large, like 8 hours.
							If you do this, you have to have more than 8 hours of data in your oplog.  This is because the secondary is going to be running 8 hours behind.

					Cons:
						As this rs member can't become primary, it can't add high availability

					Other options to undo a human error
						Replaying your oplog up to the error (probably a better option than slaveDelay)
							Assumes you know where it is.
						Use MMS backup
				
	rs.conf()
		replica set configuration file

		version field
			incremented every time there's a change to the config
				Each member of the set talks with eachother, so if one
				has a number version, they all will grab it

	Best practices for architecting RSs
		Dont have an even number of members in the set.  To make it odd you can always add in an A

		All else being equal, use RS configs that are equal to eachother

		Mixed store engine replica sets (mmapv1 and wiredTiger)
			Different members have different engines

			Replication sends operations from primary, not bytes
				This is great when different memebers have different engines, mongo versions, etc.

		When bringing a new M online, you entire dataset will go over the wire to that member.
			This will put an additional load on the network
			P will have to read its entire dataset
				This means that the P might have to place its entire dataset into RAM
			Think about doing this on off hours

		In a RS, it is best to have writes persisted onto 3 nodes (1P,2S)
		For scaling purposes, you could increase the number of secondaries and
		do reads from them individually, but if scaling is your concern, go to sharding.  This way, you can scale writes and reads.

		RSs are also good for offline analytics workloads

		If you have an RS with 3 memebers and one member is undersized, put priority:0 to make it so it never becomes primary
			In this case, if you had w: majority, that would be fine.  Because it would get acknowledgements from the 2 fastest memebers.  However if the fasters S went down, then there would be a problem.

		RS Across datacenters
			1P/1S in DC1 and 1S in DC2.  D2 will be our "diaster recovery" DC.
				This is a VERY common mongo setup.  I'd heavily look into this

				In these situations, often the S in D2 will have a priority:0.

			Can have 3M across 3 DCs
				If the P does down, the S's will talk to eachother and see that they have enough votes between the two of them to make a majority.  Then, the one with the newest data will become P.  The old P, is a S.  If there was a client its DC with a slaveOkay: true (it's okay with eventually consistent reads) then the client could get reads from that mongod

			2M Across 2 DCs
				In this case add an A into an EC2 instance, etc and make it a very small instance
					This would allow a majority to take place


	Say we have 3 mongods (1P, 2S), if 1 goes down, we can still do a
	reconfiguration of the set.
		This requires that a majority of the members of the voters are up.
		Here, we have a proper majority (2/3) so we can do it.

		rs.reconfig can be used on the primary for reconfiguration

	Arbiter
		Means that the rs member has no data (thus no replication from a primary)
		but it can participate in elections.  It will never vote for itself because
		it doesn't have any data

		For voting, can be utilized when there's an even number of RS members (and you don't need another secondary)

		Due to no data, it is a very small process so it can run on a very small
		machine or as a background process on some other instance, etc.

		Ex: 2 member rs (1P 1S)
			In this situation, how does a tiebreak occur when each member wants to
			vote for himself?  This is where the A comes in.

		Arbiter Placement
			You want the arbiter to be as close to the clients that utilize the DB as possible.  This is so the arbiter can poll the mongods on a similar connection path as the clients.  You don't want the arbiter to elect a primary that the arbiter can see but the clients can't.  So if all of the clients come through 1 DC, then place the arbiter in that DC

	Election
		Trying to get a consensus on who's going to be primary

		This can occur on a server going down or both servers coming online

		Each RS member is given 1 vote (by default).  So in a RS with 3M, 2 votes are needed to establish a consensus

		A replica set does not hold an election as long as the current primary has the highest priority value or no secondary with higher priority is within 10 seconds of the latest oplog entry in the set.

	Oplog
		All changes to RS configs 

	Cluster Wide Commit
		When doing write concerns, make sure to set a timeout, if this doesn't occur, the write will block infinitely.

		If a write has been commited on the majority of a replica set
			We can get acknowledgement of this on the clientd



		Ex: If we wanted to perform an update query to the students collection while using a write concern of 3, you might use:
			db.students.update({ _id : 3 }, { $set : { grade : "A"}}, { w : 3})
				Must get acknowledgement of write from 3 servers
				w can be "majority"
					It is best to use "majority" or another string name because what
					happens if the RS set changes?
				Use w:1 to get a basic reciept acknowledgment (this includes errors as well) At a minimum, this would be good to use.  This is the default in Mongo.
					A write concern (w: 1/1+) simply tells you if there are errors in the write, etc.  However, it doesn't guarentee that the write is persisted.  A mongod can give a write acknowledgement and then crash before the data is persisted to the journal (which will hydrate the server correctly on reboot).  
						To get around this you can set j: true (to enable journal acknowledgement).
							By default, the journal is commited to disk every 100ms.  When you specify a write with the journaled option, the journal is committed to disk in 30 ms.  In this case, if you specify j:true for every write your throughput will be a max of 33.3 writes/sec

			Capacity planning
				When utilizing a W: 1/1+, NEED to set a timeout

				It is also critical to set a max number of connections for a given mongo server.  If this number gets too high, you can start having problems with the timeout you set (above).

			Use cases for different numbers of N
				Assume replication factor of 3
				w: 0
					logging, incrementing an internal counter that the page was viewed
						If you get back an error, are you going to report this to the user?  No, so utilize w:0  Another use case would be caching
							However, there would be a problem if we go on for weeks and we don't know that an error is occuring.  We could utilize the "call every N" idea (below), and make a w:1 for those "call every N" requests.  If there is an error, we could then have something in our code to alert ops, etc.

				w: 1
					Not super critical, you care more about write availability than durability.  Another use case would be caching
					This is a way to see if basic stuff is working.  Ex: there's no dupkey violation
						A dupkey violation might occur on a unique indexed collection


				w: 'majority'
					Anything important.  You're guarenteed to have it.
				w: 3 
					More for a flow control model.  "I want to wait until all servers are caught up before proceeding".
						Maybe you are doing a bulk import.
				w : <tag>
					Ex: Provide an acknowledgement when a commit has made it to 2 datacenters (which the mongods have a identifier tag).
				"Call every N"
					Say we are inserting a large amount of data.  Every Nth insert we could make a w: majority insert, and at all other times, do a w:1.
					This helps spread out the latency.

					CON:  If we are sharded, the writes are being distributed amongst multiple mongods.  So one mongod might be having problems, but the others aren't.  Does the Nth write pick up this problem?

				Remember there might be other ways to check if all of the data has made it.  For example, at the end of a bulk load, just check the expected number of documents to the actual number, etc.

				In general, if the data is important, utilize w: majority.  If this is slow, start tuning with "call every N", etc.

				We can also do batch inserts, and then we could get an error code for the entire batch.  Did anything in the batch fail?



		Data is considered durable in Mongo if it has been commited to a majority of the servers in the RS	