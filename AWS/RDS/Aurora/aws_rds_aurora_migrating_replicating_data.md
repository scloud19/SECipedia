AR - Aurora Replica

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
      For more granularity on these steps see:
        http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Aurora.Migrate.html

      1) Determine the amount of space to provision for the cluster
      2) Use the console to create the snapshot in the region where the RDS MySQL instance is located
        If the snapshot is not in the same region as your DB cluster, use the RDS console to copy the DB snapshot to that region
      3) Use the console to migrate the DB snapshot and create an Aurora cluster with the same databases as the original DB instance of MySQL 5.6

Aurora Replication
  Monitoring Aurora replication between Primary and AR
    As read scaling and high availability depend on minimal lag time; you need to monitor how far an AR is lagging behind the primary.
      This can be accomplished with CloudWatch's ReplicaLag metric OR through a MySQL query.

      ReplicLag metric
        Because ARs read from the same cluster volume as the primary instance, this metric has a non-traditional meaning (relative to other db architectures).

        This metric for an AR indicates the lag for the page cache of the AR compared to that of the primary.

      Query mysql.ro_replica_status (table) and its Replica_lag_in_msec column
        If you query this table in your cluster, you'll get the most up-to-date information on this metric.


  Moving data between different aurora clusters
    You can set up replication between two Aurora clusters using MySQL binary log (binlog) replication.
