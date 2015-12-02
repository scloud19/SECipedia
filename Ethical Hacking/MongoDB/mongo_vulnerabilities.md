MiM = Man in the middle

Vulnerabilities
	You have to compile mongo from source utilizing an --ssl flag to have encryption on both the intracluster level and between mongo and clients.
		Must people probably don't do this.
		Ways that this connection can be MiM?

Potential Vulnerability
	How many people make their clusters authenticate with eachother via a --keyFile option?	
		Fake Mongo servers can try to access the cluster without this option and get a high level of access

	Localhost Exception
		Allows you to create access control and then create the first user in the system.
			Ex: Enable access control, connect to the localhost interface, and create the first user in the admin DB.

			The first user must have privileges to create other users (roles of userAdmin/userAdminAnyDatabase)
		
		Applies only when there are no users created in the Mongo instance.

		Vulnerabilities
			< Mongo 3.0
				Connections that gained access using the localhost exception had unrestricted access to the MongoDB instance

			Sharded cluster
				The localhost Exception applies to each shard individually as well as to the cluster as a whole.

				Ex: You create a sharded cluster and add a user administrator through the mongos.

				You must STILL prevent unauthorized access to each shard (as the localhost exception is still active on the individual shards).

				Sidestep: Disable the localhost exception at startup.

		Ways to spoof localhost exception?
		

