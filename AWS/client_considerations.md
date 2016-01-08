Naming of resources
  When giving resources unique names, do so intelligently.
    Ex: Add it any metadata that you can (region name, AZ, etc.)


Local ENV considerations
  Ex: For testing purposes, you need to connect to DB instance via MySQL workbench.  However, you are behind a firewall.
    In these situations, you need to make sure that certain ports aren't blocked on the firewall.  For example, once you set up a DB instance, you can't change its port.

IAM
  Create a virtual MFA device for each user.
  When setting up users, assign them into certain groups (admins/devs/DBAs/etc)

NAT
  If you're building a NAT (ex. interface from a public subnet to a private)
    Make sure that you have some type of redundancy.  Otherwise, if the NAT goes down, you're toast.

VPC
  Launch everything into a VPC (including RDS services, etc.)

  Use a different subnet for staging/dev/prod

  When creating a subnet, MAKE SURE to take into account future node considerations and AWS subnets (and their CIDR blocks) can't be changed.

RDS
  If using a mySQL db, should the client use Amazons MySQL compatible Aurora DB engine? What are the costs/benefits?


  What are the memory and processor requirements?
    Use this to inform: http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Concepts.DBInstanceClass.html

  Aurora
    If migrating data into Aurora, see the migrating_data doc in the Aurora directory (in my notes)

    Differences between AWS RDS Aurora and RDS for MySQL
  See the end table in http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Aurora.html
  
    Very important implications for failover
      Amazon Aurora Replicas can act as a failover target with NO data loss, where MySQL replicas can act as a failover target, but there's potentially minutes of data loss.

      Check out this for other important implications on why Aurora is the best
        https://aws.amazon.com/rds/aurora/faqs/

      HUGE PRO: DB automatically grows as the amount of data in your DB increases

    Make sure to connect to the cluster DNS (which always has "cluster" in the DNS name).  This DNS will always point to the primary.

    Route write queries to the cluster DNS name (which always points to the primary) and distribute read only queries to the Read Replica nodes in the cluster.
      In regards to the read replica nodes; what occurs during failover of that unique node? Since we are routing the traffic to that individual node, does this get rerouted to another unique read replica?

      If the client can use the following SQL statements, these are performance gains in Aurora
        Aurora accelerates parallel inserts sorted by primary key and applies to LOAD DATA, INSERT INTO ... SELECT ... statements.

    When onboarding a DBA, dont give them the master password.  Make a user from them in IAM with a database admin policy.  These individuals can then go into MySQL and create accounts through the database, etc.
      Also, to through aws_rds_general_aurora.md/Security and bring up any relevant points that they need to know

Utilize ec2 reserved instances for a base (cost efficiency) and then utilize on demand servers to scale.
  You can also leverage spot instances in a similar vein.

  You can also launch spot instances with a required duration, which are not interrupted due to changes in the Spot price.

If running large scale NoSQL DB on EC2 instance
  Look at provisioned IOPS SSD EBS

Do consolidating billing for separate ENV's, etc.?
  Look at consolidated billing file

For different ENVs, projects, etc. make sure to create tags and then aggregate them via resource group.
  Look at aws_misc

  Helps for keeping track of various departments, etc.

EBS
  Snapshots (and general storage considerations)
    After creating a volume, you can create a snapshot for backup purposes
    
    Another use case: You can create your whole dev/QA ENV on magnetic storage, and when you are ready to transition QA over to PROD, you can take a snapshot of the volume and then create a volume from the snapshot (and set the storage type to SSD for example)
 
EC2 instance
    Instances that access other instances through their public NAP IP are charged for regional or Internet data transfer, depending on whether the instances are in the same region.
    For cost effectiveness, look at ways around this.
      Launching into all of those instances into a VPC?


    Elastic Network Interface (ENI)
      Make a secondary private IP for incoming traffic on an ENI
        In the event of an instance failure, you can move the interface and/or secondary private IP address to a standby instance.

  Recommended security groups for web servers/db servers
    http://docs.aws.amazon.com/es_es/AmazonVPC/latest/UserGuide/VPC_Scenario3.html
  Set a reminder to run a yum update.
    Need to make sure that there isn't any breaking changes

    Another solution
      Run a yum update as a chron job with lock-on-launch

        If AWS says that these types of updates aren't breaking, this is the way to go.  Then, for the next release, look at documentation to see if there's any breaking changes, etc.


AMIs
  If you need a type of government compliance, etc. look for prebuilt images

  Create a base AMI and then distribute to different Regions for fault-tolerance and scalability
    If it's encrypted, see EC2 notes for caveat



Regions
  Legal considerations
    Look at what regions you're launching into and their legal considerations.

  Latency 
    Make sure that the region you choose is close to your customers (and other portions of the application) Ex: Have your DBs in the same region as your app servers, etc.
        

CloudWatch
  Google common setups for Cloudwatch
    Look for example in cloudwatch.md

  Look at setting alarms for when CPU utilization is below a threshold for 24 hours
    Maybe we can stop the instance to save the client money?

  In general, set alarms for
    stop, terminate, reboot, recover instances.
      Importance: We need alarms for if an instance fails, etc.
      Look up best practices for these alarms/actions

  Once I find the metrics that I need to follow, find their location at
    http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/monitoring_ec2.html

CloudFront
  What kind of CDN considerations do they need?  This dramatically increases load times for users.