Important Security Links
	https://docs.mongodb.org/manual/security/?_ga=1.140501363.1940964481.1446699147

Key Topics
	authentication
		Turned off by default
			mongod --auth to enable
			mongos --auth
				You can place this in the config file
		logging into the system

		Can be done in Mongo using
			Challenge/Response
			X.509 certificates (SSL style certs)

			Enterprise edition
				Kerberos
				LDAT

	access control/authorization
		Once you are logged in, what can you do?

	admin database
		Houses users/roles

Security
	Security strategies
		"Trusted Environment"
			Locking down ports at the network layer so Mongo isn't publically accessible
		mongoDB authentication
			use these flags on the CLI
				--auth
					securing client connections and access
				--keyFile
					intracluster security/communication
						Machines in the cluster authenticating amongst themselves

						To run Mongo with encryption over the wire, you need to compile mongodb with --ssl.
	Encryption at rest
		Third party product available
