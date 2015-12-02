Key Takeaways
	ALWAYS use journaling in PROD on mongod, etc.

Definitions
	Ran hot
		You can do something in prod while the server is doing normal operations/load

Backup + Restore
	Methods for an individual replica set OR an individual server
		mongodump/mongorestore

			mongodump --oplog
				--oplog
					Use only if you're utilizing a RS
				make a dump of all databases on a given server OR a particular database

				This can be ran hot
					but it's best to do this on offhours

				Generates BSON files

			mongorestore --oplogReplay
				--oplogReplay
					Will allow you to achieve a true point in time snapshot of this particular RS that you're dumping

		filesystem snapshot
			Good way to do a hot backup

			IMPORTANT: Need journaling enabled on mongo instances
				In general: If the mongo instance in question is cleanly shutdown, there won't be any files in /data/journal
					But, when you're doing a point in time snapshot, this DIR should have entries in case of a crash.

				If journaling wasn't enabled, whats if you created the snapshot when an operation to disk was only partially completed?

			RAID config with snapshot
				db.fsynclock()
					..Then do all snapshots of sub-volumes in the RAID config..

					While locked, you may not be able to do queries.
						Thus this operation would need to be quick.
							Traditionally, snapshots are very quick ~1 sec


					This method flushes all writes to disk, and then locks down the sub-volumes from further writes.

					If you can snapshot your whole volume (not just the individual sub-volumes), then this isn't necessary.
						For AWS EBS, you don't have the ability to snapshot the whole RAID volume.

				db.fsyncUnlock()
					Do this when finished






			Ex: AWS EBS snapshot, etc.

		backup from secondary
			if secondary isn't used in PROD
				You can shut it down, copy all of its files, and then restart it (it will now catch back up)

		Other options (use other options first)
			mongoimport
				Allows you to import a non-BSON file into Mongo
					Ex: JSON, CSV

	Backing up a sharded cluster
		We need to
			Back up each shard individually
				If doing RS, you only need to do one member
			Back up the config server
				In PROD, there are usually multiple config servers, but they mirror eachother, so you'd only need to backup one.
		
		Backup Process
			This process won't give you a true point-in-time backup of the cluster.
				Thus, you'll have all the data from the time you STARTED the backup, but the data that was written WHILE the backup was occuring is questionable.  (Maybe it's there.. maybe not)

				it might give you a point-in-time of an individual shard's RS member
					Ex: mongodump --oplog

			Before doing this, really check to make sure the cluster is healthy


			1) Turn off balancer
				This moves data around.  If there are chunk migrations going on during the backup this could be problematic

				Ex: sh.stopBalancer()
					Stops the balancer and waits for all chunk migrations to finish

				Ex:
					mongo --host SOME_MONGOS --eval "sh.stopBalancer()"

					// Make sure that this worked...
					// So you should just log in to shell, but this is good to see --eval being used.

			2) Backup config database
				Options
					mongodump --db config

					Stop 1 of your config servers and just copy one of its files

				Ex: mongodump --host MONGOS_OR_CONFIG_SERVER --db config
					The mongos will point to the config server naturally

			3) Back up each shard
				You only need to do one member of the RS
					Make sure that the member is healthy and that it isn't lagging (its writes) by a large amount
				Reference the above:
					Methods for an individual replica set OR an individual server 

				Ex: mongodump --host shard1_svr1 --oplog /backups/cluster1/shard1

				mongodump --host shard2_svr1 --oplog /backups/cluster1/shard2

				// Do this pattern for all shards
				// You'd usually want to pull in the dump from the secondary

				// You can also do all of these operations in parellel

			4) Once you finish this process turn back on the balancer.
				For normal backup use cases, the amount of time that the balancer would be off is OKAY

				Ex: mongo --host some_mongos --eval "sh.startBalancer()"

	Restore Sharded Cluster
		If everything needs to be restored
			Take all machines offline, migrate in datafiles, then put everything back online	 

