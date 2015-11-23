Bad practice to have databases open to the public.
	Open them up to a private VPC and create security groups and roles based on that
		Make sure to create your own security group for the DB

Relational (Online Transaction Processing-OLTP)
	Scenario:
		What if we have an EC2 instance connecting to an RDS mySQL db?
			We need to connect the security group of the EC2 instance (think of outbound) to the security group of the rds service (think of inbound)
				We accomplish this in a VPC
					This step is needed so the two items can communicate with eachother
					Look at VPC.md

	Relational Database Service (RDS)
		Has the ability to add a managed memcached or redis-compatible in-memory cache
		to speed up your database access

		MySQL
			You can resize your instance (cpu/HD) on the fly.

		Microsoft SQL Server
			Can't change the HD space on the fly
				You either initially create the db with the appropriate HD space
				for future endeavors OR once you get to a certain theshold you
				export all of database and create a new one with increased storage
					CON: Outage when migrating over DB
			When created, it doesn't automatically have a db  

		You can change the password

		Resore DB instance to a point in time
			If you do this, it will create an entirely new instance with a new endpoint

		Amazon doesn't give you a public/private IP for your DB, you get an DNS endpoint
		You only have control over the DB, not the server itself
			EX: You can't patch, ssh or rdp into the underlying server, etc.  You can say when the maintenance will occur, but that's pretty much it

		Maximum backup window (time to keep a backup snapshot) 35 days

		Multi-AZ deployment
		AWS will maintain a synchronous standby replica in a different AZ than the DB instance.  This will automatically fail over to the standby if needed
			Only MySQL can be utilized for read replicas
		MySql

		Sql Server
			From Microsoft, you can bring your own license
		Postgres
		Oracle
			You can bring your own liceneses or you can use Amazon's licenses
		Aurora
			AWS's proprietary DB that's mySQL compatable.
			Up to 5x better performance than mySQL at a price point one tenth
			that of a commercial database

			Scaling
				Start with 10Gb and scales in 10Gb increments to 64Tb
				Compute resources can scale up to 32vCPUs and 244Gb of Memory

			Availability
				2 copies of your data is contained in each AZ, with a minimum
				of 3 AZ (6 copies of data)
					This is great from a redundancy perspective but you can
					also to reads from this nodes as well

				Aurora is designed to transparently handle the loss of up to 2
				copies of data without affecting database write availability and
				up to three copies without affecting read availability.

			Aurora storage is also self-healing. Data blocks and disks are continuously
			scanned for errors and repaired automatically.

		Setting up an RDS instance
			Create a security group
	NoSQL
		DynamoDB
			vs Mongo
				You can't have embedded data structures in Dynamo (key value pairs aka objects).
			Always stored on SSD 

			Creating an instance
				Hash Attribute Key
					Primary Key, can be UserID, etc.

			Spread Across 3 geographically distinct data centers automatically
				Eventually Consistent Reads (Default)
					Consistency across all copies of data is usually reached within a second.  So if the data is written and then read from another datacenter, there should be a roughly second delay.
						When do you do query the DB, there might be a chance that the query is routed to an AZ datacenter that isn't updated (~second delay)
						This gives best read performace vs. Strongly Consistent Reads
				Strongly Consistent Reads
					Returns a result that reflects all writes that received a successful response prior to the read
						I.e. If a write 
					This is a more-or-less gaurentee that the data isn't stale when it's read
			Pricing
				Wont need to do calculations on the exam, but just remember the main breakdown on how pricing in Dynamo is done (read/write throughput capacity and Storage)
				Provisional Throughput Capacity
					Write Throughput $.0065 per hour for every 10 units
					Read Throughput $.0065 per hour for every 50 units

					Read Unit - 1 read a second
					Write Unit - 1 write a second
				Storage costs
					$.25Gb/month 

				Ex: Assume that your application needs to perform 1 million writes
				and 1 million reads per day, while storing 3 GB of data.

					11.6 writes/reads per second
						Need 12 read units
						Need 12 write units

					On-demand pricing in US East Region
						12 Write Capacity Units would cost $.1872 per day
						12 Read Capacity Units would cost $.0374 per day
							$.2246 per day

					Storage costs $.25 per GB per month
						Assuming a 30-day month, your 3 GB would cost you
						3 * .25 / 30 = $.025/day

					So
					.2246 + .025 = $.2496/day or $7.50/month 

	Data Warehousing Databases (Online Analytics Programming-OLAP)
		These used to be relational in structure, but now these are more of their own types

		Used for business intelligence and large scale reporting
			Ex: Give me the net profit for all apple computers in Japan
				Huge numbers of inputs need to go into this calculation

		Redshift
			COlumnar Data Storage
				Instead of storing data as a series of rows, Redhsift organizes
				the data by column.
					Row-based systems
						Ideal for transaction processing
					Column-based systems
						Ideal for data warehousing and analytics where queries
						often involve aggregates performed over large data sets.
						Since only the columns involved in the queries are 
						processed and columnar data is stored sequentially on the storage media, column-based systems require far fewer
						I/Os, which greatly improces query performance.

						Advanced Compression
							Because of the sequential way that the column-based data
							is stored on disk.

							The data-types are exactly the same in each column

							Redshift doesn't require indexes or materialized views
							thus it uses less space

							As data is loaded into an empty table, Redshift automatically samples your data and selects the most appropriate compression scheme
			Backup
				The whole cluster can only be in one AZ,
					You can restore a snapshot to a new AZ in the event of an outage
			Security
				Encrypted in transit using SSL
				Encrypted at rest using AES-256 encryption
					Key managament by default is taken care of
					You can manage your own keys through Hardware Security Modules (HSMs) or AWS Key Management Service (if needed)

			Pricing
				Compute Node Hours
					aka: Total number of hours you run across all your compute nodes for the billing period

					You are billed for 1 unit per node per hour

					Ex: 3-node data warehouse cluster running persistently for an entire month would invur 2,160 instance hours
						You wont be charged for leader node hours, only compute nodes.
							You will pay if you are using 1 node (by default this combines a leader node and a compute node)
				Backup

				Data transfer
					Only within a VPC, not outside it

			Massively Parallel Processing (MPP)
				Automatically distributes data and query load across all nodes.
			Can start with a single node
				160 Gb

				Would include the functionality of a compute node and a leader node
			Can scale to multi-node
				In these configs
					leader node - Managaes client connections and receives queries
					compute node - Store data and perform queries and computations
						Max: 128 Compute Nodes  

			10x faster than traditional solutions



	ElatiCache
		Web service that makes it easy to deploy, operate, and scale an in-memory cache in the cloud

		If you have a service that is making repeated calls to the db, then we can cache that information via elastiCache.
			Significantly improves latency and throughput for many read-heavy application workloads
				Ex: social networking, gaming, media sharing, Q&A portals
			Compute-intensive workloads
				Ex: recommendation engine

		ElastiCache supports
			Memcached
				Widely adopted memory object caching system
				ElastiCache is protocol compliant with memcached, so popular tools
				that you use today with existing memcached envs will work with the
				ElastiCache service
			Redis
				Open source in-memory key-value store that supports data structures
				such as sorted sets and lists.

				Redundancy
					ElastiCache supports Master/Slave replication

					Allows Multi-AZ redundancy

