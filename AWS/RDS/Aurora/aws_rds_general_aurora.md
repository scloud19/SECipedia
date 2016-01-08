Allowed SQL statements
  As of 1/8/2015, you can execute any SQL command that is compatible with MySQL version 5.6
    There are small caveats (as you'll see in other areas of these notes)


Aurora
  Security
    Securing Aurora with SSL
      If secured via SSL, the public key is held at
      http://s3.amazonaws.com/rds-downloads/rds-combined-ca-bundle.pem
      
      Aurora clusters support SSL connections from applications using the same process and public key as the RDS MySQL DB instances.

      RDS creates an SSL cert and installs the cert on the DB instance when the instance is provisioned.

      The SSL cert includes the DB instance endpoint as the Common Name (CN)
        This helps to guard against spoofing attacks

      To encrypt connections using the default mysql client, launch the mysql client using the --ssl-ca parameter to reference the public key, for example:
        mysql -h mycluster-primary.c9akciq32.rds-us-east-1.amazonaws.com --ssl-ca=[full path]rds-combined-ca-bundle.pem --ssl-verify-server-cert

      You can use the GRANT statement to require SSL connections for specific users accounts. For example, you can use the following statement to require SSL connections on the user account encrypted_user:
        GRANT USAGE ON *.* TO 'encrypted_user'@'%' REQUIRE SSL

      More information for SSL connections in MySQL
        http://dev.mysql.com/doc/refman/5.6/en/secure-connections.html


    MySQL Level permissions
      In addition to IAM RDS policies, you can do permissions inside Aurora (as you could for any MySQL db).
        Thus you can utilize commands such as:
          CREATE USER, RENAME USER, GRANT, REVOKE, SET PASSWORD, etc.

      Master User
        When you create an Amazon Aurora DB instance, the master user has the following default privs:
          alter
          alter routine
          create
          create routine
          create temporary tables
          create user
          create view
          delete
          drop
          event
          execute
          grant option
          index
          insert
          lock tables
          process
          references
          replication slave
          select
          show databases
          show view
          trigger
          update

      
      rdsadmin user
        Is used via AWS for admin purposes.  You can't change items related to this account.

      DB cluster management
        kill, kill_query commands have been restricted on AWS.
          Instead, utilize rds_kill, rds_kill_query to terminate user sessions or queries on DB instances.




  Performance Enhancements
    Fast Insert
      Accelerates parallel inserts sorted by primary key and applies to LOAD DATA, INSERT INTO ... SELECT ... statements.

      Accomplishes the performance gain by caching the position of the cursor.

      Can see metrics with fast inserts via
        SQL Statement: show global status like 'Aurora_fast_insert%'; 


  Reliability
    "Survivable" Cache Warming
      Aurora "warms" the buffer pool cache when a database starts up after it has been shut down or restarted after a failure.
        In other words, Aurora preloads the buffer pool with the pages for known common queries that are stored in an in-memory page cache.
  
  Failover
    Amazon Aurora Replicas can act as a failover target with NO data loss, where MySQL replicas can act as a failover target, but there's potentially minutes of data loss.

    Check out this for other important implications on why Aurora is the best
      https://aws.amazon.com/rds/aurora/faqs/
      
    During a failover, Aurora continues to serve requests, with minimal interruption of service, to the cluster endpoint from any available instances as it replaces the failed instance.
      Ex: An Aurora Cluster that has a primary, and 2 replicas; each in their own AZ.
        If the primary's AZ goes down, a replica will be promoted to primary and the DNS will be rerouted to that primary.

  AWS's proprietary DB that's mySQL compatible.  It is meant to be a drop-in replacement for MYSql.

  Pricing
    Storage
      You only pay for what you use.

  Up to 5x better performance than mySQL at a price point one tenth
  that of a commercial database
    Amazon Aurora delivers significant increases over MySQL performance by tightly integrating the database engine with an SSD-based virtualized storage layer purpose-built for database workloads, reducing writes to the storage system, minimizing lock contention and eliminating delays created by database process threads. Our tests with SysBench on r3.8xlarge instances show that Amazon Aurora delivers over 500,000 SELECTs/sec and 100,000 updates/sec, five times higher than MySQL running the same benchmark on the same hardware. Detailed instructions on this benchmark and how to replicate it yourself are provided in the Amazon Aurora Performance Benchmarking Guide.

  Scaling
    Start with 10Gb and scales in 10Gb increments to 64Tb
    Compute resources can scale up to 32vCPUs and 244Gb of Memory

  Availability
    2 copies of your data is contained in each AZ, with a minimum
    of 3 AZ (6 copies of data)
      This is great from a redundancy perspective but you can
      also get reads from these nodes as well

      Z: Is the minimum AZ still 3? It might now be 2.

    Aurora is designed to transparently handle the loss of up to 2
    copies of data without affecting database write availability and
    up to three copies without affecting read availability.

  Aurora storage is also self-healing. Data blocks and disks are continuously scanned for errors and repaired automatically.

  Administration
    AWS RDS provides these services
      provisioning, patching, backup, recovery, failure detection, and repair.

    Migration tools
      Are provided to convert your existing AWS RDS MySQL to Aurora.

    
