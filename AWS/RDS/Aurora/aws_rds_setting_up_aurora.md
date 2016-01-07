Read the aws_rds_setting_up.md before doing anything in here.
  For setting up the VPC and Security Groups, the Aurora specific information will be in this guide.




  As of 1/4/2016- All VPC and EC2 resources that you use in your Amazon Aurora DB cluster must reside in US East (N.Virginia), Us West (Oregon), EU (Ireland), Asia Pacific (Tokyo)

  Endpoints in the cluster
    Cluster endpoint
      Ex: *.cluster-123456789012.us-east-1.rds.amazonaws.com:3306
        Notice the "cluster" identifier in the name.
        This connects you to the primary instance for the DB cluster.
          The difference between this endpoint and the primary's unique endpoint (see below), is that the cluster endpoint will always point to the primary in failover situations.
        You can perform both read/write operations to this endpoint

    Unique endpoints
      There's one for the primary instance and each Replica

      If certain clients are do only read operations, you can have them connect to the replica's unique endpoint to spread the read load.
        These read operations are the READ only queries.

      Don't contain "cluster" in the endpoint dns.
        Ex: *.123456789012.us-east-1.rds.amazonaws.com:3306

    When you connect to an aurora endpoint, you are connecting to the primary instance.
      As this is the primary instance, you can do both read and write operations to the endpoint.

  When you create an Aurora instance, you are actually creating a DB cluster.
    DB cluster
      Consists of
        1 or more instances AND a cluster volume


        Aurora Instances
          Each DB cluster can have up to 15 Aurora Replicas in addition to the Primary Instance (16 total).

          Two types of instances can be in a Aurora DB Cluster
            Primary Instance

              Supports read-write workloads.
              Performs all of the data modifications to the cluster volume.

              Each DB Cluster has one primary instance

              Replicates its writes to each availability Zone that the replicas are in.
                All of the AZ's data copies, as a whole, are considered the cluster volume.

            Aurora Replica
              Supports only read operations.

              By placing the Replicas in separate AZs, you increase db fault tolerance.

              You can have have multiple replicas in one AZ.

              Aurora also supports replication with an external MySQL database, or an RDS MySQL DB instance.  When using Aurora, the RDS MySQL DB instance must be in the same region.







        An Aurora Cluster volume
          A cluster volume manages the data for the aurora instances. 
            The volume is seen as one volume

          Storage Auto-Repair
            Aurora maintains multiple copies of your data in three AZs

            There's automatic detection of failures in the disk volumes that make up the cluster volume.
              When a segment of a disk volume fails, this segment is automatically repaired
          

          Utilizes SSD drives

          A cluster volume consists of copies of the data across multiple AZs in a single region.
            This data is automatically replicated across the AZs; thus it's highly durable.

          Automatically grows as the amount of data in your DB increases. 

          A virtual database storage volume that spans multiple AZs
            Each AZ has a copy of the cluster data.

            A given Aurora replica, in a given AZ, reads from the data that's stored in its AZ.  

  Building the cluster

    Prereqs
      To build the cluster, you need a VPC with at least two subnets in at least 2 AZ's.  This will distribute your Aurora instances into these AZ's.
        As an aside, the cluster volume will always span three AZ's.

        Before the cluster created, you need to have an appropriate VPC.  If you're utilizing a VPC that you created yourself (i.e., not the one that Aurora automatically builds for you), read:
           http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Aurora.CreateVPC.html

           Basically, 3 items need to get done
            Create/Utilize a VPC with atleast 2 subnets in 2 AZs.

            Specify a VPC security group that authorizes connections to the cluster.

            Create an RDS DB subnet group that defines at least two subnets in the VPC that can be used by the Aurora DB cluster

      Additional considerations
        Make sure that IAM has provisioned the appropriate policies that grant permissions required for RDS operations.

        Remember that all instances in the DB cluster use the same port.

      Creating a cluster primary using the console
        When interacting with the RDS creation wizard, you'll come across some of the following important options
          DB Instance Class: Selects the instance class for the PRIMARY.

          Multi-AZ Deployment: Has to do with creating Aurora Replicas in other AZs for failover support.

          DB Instance Identifier:  Is a name for the primary instance in the DB cluster.  This name will be used in the endpoint address for the primary instance of the cluster.

          DB Cluster Identifier:  This will be used in the cluster endpoint address.

          Master Username:  Will create a user that has the following SQL privs:
            create, drop, references, event, alter, delete, index, insert, select, update, create temporary tables, lock tables, trigger, create view, show view, alter routine, create routine, execute, create user, process, show databases, grant option.

        Availability Zone: If you WANT to specify a particular AZ for launching into.

        Enable Encryption: Enables encryption at rest for the DB cluster.

        Enable Enhanced Monitoring: Gives you statistics for the operating system that your DB cluster runs on.

        Granularity:  Only available if enhanced monitoring is set to yes.
          The number specified for granularity, in an interval, in seconds for metric collection.


      Adding Read Replicas to the cluster
        You can add up to 15 Aurora Replicas by using the Create Aurora Replica wizard

        Select the primary instance for your cluster in the console -> Instance Actions -> Create Aurora Replica

        Notable settings in the creation wizard
          Availability Zone
            This list includes only those AZs that are mapped by the DB subnet group was specified earlier in the VPC creation.








