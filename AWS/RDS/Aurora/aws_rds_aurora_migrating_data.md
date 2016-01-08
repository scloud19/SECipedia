Migration options into Aurora
  Overall options
    1) Amazon RDS MySQL snapshot to Aurora.

    2) From a MySQL instance running externally to Amazon RDS.
      To do it this way, you can set up replication between your MySQL database, and an Amazon Aurora DB cluster.  
        To do this, it is recommended that the MySQL database runs MySQL version 5.5 or later.

  Option 1
    The DB snapshot must have been made from an RDS instance running MySQL 5.6

    Once the DB cluster is created, you then can create the Aurora Replicas.

    General Steps
      

