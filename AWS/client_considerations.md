Utilize ec2 reserved instances for a base (cost efficiency) and then utilize on demand servers to scale.
  You can also leverage spot instances in a similar vein.

  You can also launch spot instances with a required duration, which are not interrupted due to changes in the Spot price.

If running large scale NoSQL DB on EC2 instance
  Look at provisioned IOPS SSD EBS

EBS
  Snapshots
    After creating a volume, you can create a snapshot for backup purposes
    
    Another use case: You can create your whole dev/QA ENV on magnetic storage, and when you are ready to transition QA over to PROD, you can take a snapshot of the volume and then create a volume from the snapshot (and set the storage type to SSD for example)
 
EC2 instance
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