Read the aws_rds_setting_up.md before doing anything in here.
  For setting up the VPC and Security Groups, the Aurora specific information will be in this guide.



  As of 1/4/2016- All VPC and EC2 resources that you use in your Amazon Aurora DB cluster must reside in US East (N.Virginia), Us West (Oregon), EU (Ireland), Asia Pacific (Tokyo)

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

              The replicas distribute the read workload, and by placing the Replicas in separate AZs, you increase db availability

              You can have have multiple replicas in one AZ.




        An Aurora Cluster volume
          A cluster volume manages the data for the aurora instances. 

          A virtual database storage volume that spans multiple AZs
            Each AZ has a copy of the cluster data.

            A given Aurora replica, in a given AZ, reads from the data that's stored in its AZ.  

  
