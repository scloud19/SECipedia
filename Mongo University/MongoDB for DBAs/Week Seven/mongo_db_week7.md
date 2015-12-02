Additional Security Information
	mongo_vulnerabilities.md

General Tips
	Moving between dbs on the system
		Ex: 
			db.getSisterDB('admin').system.users.find()

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

				Write permissions
					Also allow you to create an index

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
				
				Ex: Creating a user in admin DB via the localhost exception

					mongod --auth
					// Start mongod

					mongo --host localhost
					// COnnect into shell via localhost exception

					use admin

					var me = {
						user: 'zach',
						pwd: 'ssh',
						roles: ['userAdminAnyDatabase', 'readWriteAnyDatabase']
					}
					
					db.createUser( me )

					// Note: This is in the context of the admin DB.  (We used "use admin")

					// This makes it possible to potentially have the same username across different databases but give them different passwords.

					// Once we create the user our localhost exception is gone.  So we can reconnect with the shell (as the user) or...

					db.auth("zach", "ssh")

					// Now we can access the admin db in the same mongo shell.

				Ex: authenticating with the user
					mongo localhost/admin -u zach -p ssh

				Ex:


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

							All cluster members must have this same file

							Allows base64 legal characters

							Ex: Creating a keyfile

								touch keyfile
								chmod 600 keyfile

								// could use your own password for keyfile, or create one

								openssl rand -base64 60 >> keyfile


						Machines in the cluster authenticating amongst themselves

						To run Mongo with encryption over the wire (and for intra-cluster communication), you need to compile mongodb with --ssl.
							Ex: scons all --ssl
								// Need to build mongo from source

						When working with a replica set or sharded cluster
							in addition to the "--auth" you will pass a "--keyFile FILE_NAME"
								This makes --auth more explicit

								ex:
								mongos --auth --keyFile FILE --replSet HEY
								mongod --auth --keyFile FILE

								
	Encryption at rest
		Third party product available
