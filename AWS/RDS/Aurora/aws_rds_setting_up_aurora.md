Read the aws_rds_setting_up.md before doing anything in here.
  For setting up the VPC and Security Groups, the Aurora specific information will be in this guide.

  As of 1/4/2016- All VPC and EC2 resources that you use in your Amazon Aurora DB cluster must reside in US East (N.Virginia), Us West (Oregon), or EU (Ireland) regions.

  When you create an Aurora instance, you are actually creating a DB cluster.
    DB cluster
      Consists of
        1 or more instances

        Cluster volume
          A cluster volume manages the data for those instances. 

          
  
