Utilize ec2 reserved instances for a base (cost efficiency) and then utilize on demand servers to scale

If running large scale NoSQL DB on EC2 instance
  Look at provisioned IOPS SSD EBS

EBS
  Snapshots
    After creating a volume, you can create a snapshot for backup purposes
    
    Another use case: You can create your whole dev/QA ENV on magnetic storage, and when you are ready to transition QA over to PROD, you can take a snapshot of the volume and then create a volume from the snapshot (and set the storage type to SSD for example)
 
AMIs
  If you need a type of government compliance, etc. look for prebuilt images

Regions
  Legal considerations
    Look at what regions you're launching into and their legal considerations.
        

  