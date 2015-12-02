Important Security Links
	https://docs.mongodb.org/manual/security/?_ga=1.140501363.1940964481.1446699147

Definitions
	namespace
		DB_NAME.COLLECTION_NAME

Key Topics
	authentication
		Turned off by default
			mongod --auth to enable
			mongos --auth
				You can place this in the config file
		logging into the system


		Can be done in Mongo using
			Challenge/Response
				I.e. username/password
			X.509 certificates (SSL style certs)
				Can be used for intracluster authentication (similar to the private key)
			Enterprise edition
				Kerberos
				LDAT

	access control/authorization
		Once you are logged in, what can you do?

	Users
		Types of users (clients)
			admin
				Can do administrative commands that are server wide

				These are users who are created in the admin db 

			regular
				Access a specific db
				read/write or readOnly

		Roles
			The localhost exception
				This exception (on this connection) only allows for the creation of the first user on the admin DB
			In addition to predefined roles, you can create custom ones as well.

			some of the roles available (you can have muliple):
				specific to the DB the user was created in
					read
					readWrite
					dbAdmin
					userAdmin

				Misc role
					clusterAdmin
						adding a shard, managing a replica set, etc.

				Any DB
					readAnyDatabase
					dbAdminAnyDatabase
					userAdminAnyDatabase
						Create users, etc.
					readWriteAnyDatabase

			DB level
				DB_NAME.system.users
			Server/Cluster Level
				"admin" db

				Ex: to find users for server/cluster
					use admin
					db.system.users.find()
				
				Ex: Creating a user in admin DB
					use admin

					var me = {z
						user: 'zach',
						pwd: 'ssh',
						roles: ['userAdminAnyDatabase', 'readWriteAnyDatabase']
					}

					// These roles are supported by Mongo.
					
					db.createUser( me )

				Ex: authenticating with the user
					mongo localhost/admin -u zach -p ssh


Security
	Security strategies
		"Trusted Environment"
			Locking down ports at the network layer so Mongo isn't publically accessible
		mongoDB authentication/authorization
			use these flags on the CLI
				--auth
					securing client connections and access
					(authentication/authorization)
					This is communication is NOT encrypted in this option without compiling with ssl option
						The initial handshake (where credentials are shared) IS encrypted though

					Ex: Creating client authentication and adding a user via localhost exception 

						mongod --auth
						// Start mongod

						mongo --host localhost
						// Connect into shell via localhost exception

						// In shell...

						use admin
						db.addUser("the_admin", "PASSWORD")

						// Now our localhost exception is gone and we need to authenticate to do anything

						db.auth("the_admin", "PASSWORD")

						// Note: This is in the context of the admin DB.  (We used "use admin")
						// This makes it possible to have the same username across different databases but give them different passwords.





				--keyFile FILE_NAME
					This flag works on mongod/mongos
					intracluster security/communication
						Is NOT encrypted with this option (without compiling with ssl)
						This makes sure that all of the members of the cluster are legitimate
							The initial handshake (where credentials are shared) IS encrypted though

							This is important because if the server is a member of the cluster, you have a high level of access.

						FILE_NAME
							contains a shared secret key
							the cluster members use this to cross-authenticate with eachother

						Machines in the cluster authenticating amongst themselves

						To run Mongo with encryption over the wire (and for intra-cluster communication), you need to compile mongodb with --ssl.
							Ex: scons all --ssl
								// Need to build mongo from source

						When working with a replica set or sharded cluster
							in addition to the "--auth" you will pass a "--keyFile FILE_NAME"
								This makes --auth more explicit

								ex:
								mongos --auth --keyFile FILE
								mongod --auth --keyFile FILE

								
	Encryption at rest
		Third party product available
