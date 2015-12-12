Key terms
	AZ - Availability Zone

Additional items to do for exam


General Tips/Info:
	Use DNS names whenever possible, AWS can switch IPs

	The URL in which to access instance metadata
		curl http://169.254.169.254/latest/meta-data/

		Get public IP
			curl http://169.254.169.254/latest/meta-data/public-ipv4
	

	Helpful linux commands
		service httpd status
			check the status of apache

		service httpd start
			start apache

		chkconfig httpd on
			whenver machine restarts, it will start apache

EC2
	
	Instance Pricing
		On Demand Instances
			Fixed hourly pricing

		Reserved Instances
			1 year or 3 year terms.

			There's a significant discount on the hourly rate for these types of commitments
			Can pay upfront or as you go

			Ex: You can have the reserved servers as a base and utilize the on-demand servers for scaling groups

		Spot Instances
			Enable you to bid whatever price you want
			
			Only use this if your application has flexible start and end times

			You set a maximum price that you're willing to pay, and if the current price is under that, you get the instance.  However, if at any time the prices exceeds your maximum price, then your instance is lost.
				You need to be writing to something with durability (i.e. s3 bucket, etc)

			Look at certain times for more competitive rates
				Ex: Sunday morning between 2-4 am

	Different Types of Instances
		General Purpose

		Compute Optimized
			For compute intensive applications
		
		Memory Optimized
			Database & Memory Caching Applications
		
		GPU Instances
			High performance parallel computing
				Ex: Hadoop
		
		Storage Optimized
			Data warehousing and Parallel Computing

	Storage Options
		Local Instance Storage
			non-persistent
		
		Elastic Block Store (EBS)
			persistent

			EBS' are tailored for EC2 utilization

			You cant mount the same EBS storage into multiple Ec2s
				Only 1-to-1 relationship

			Storage Options
				General Purpose SSD
					99.999% availability
						3 IOPS per GB
					 	
					 	Offer single digit ms latencies, and also have the ability to burst up to 3000 IOPS for short periods
				
				Provisioned IOPS SSD
					I/O intensive apps like large NoSQL dbs 
				
				Magnetic
					Lowest price/GB
					
					Ideal for workloads where the data is used infrequently
						Ex: Different types of config docs, etc.
	
	AMIs - Amazon Machine Image
		Amazon Linux AMI
			Comes with a lot of items, including the AWS command line.  Very helpful because you don't have to install the CLI every time you boot up a new instance

	Configuring an instance
		Steps (in AWS console)
			3 - Configure Instance
				Subnet - Is the availability zone for the instance

				Shutdown behavior (Stop or Terminate)
					When you shutdown the instance, AWS can stop it (and the instance will persist) or terminate (the instance is deleted)

				Advanced Details
					Are bootstrap scripts that will be run in your EC2 instance when the instance is created
						Ex: Bash script
			
			4 - Add Storage
				You can attach additional EBS volumes after launching an instance but at this point, you can't add instance store volumes

			6 - Configure Security Group
				Firewall rules that control the traffic for your instance.
					Rules with source 0.0.0.0/0 allow all IP addresses to access your instance.

					It is recommended to allow access from known IP addresses only.
			
			7 - Review
				Select an existing key pair or create a new key pair
					Key pair
						Public Key
							Like a padlock and you can have many different copies of it.  You can place the public key in many EC2 instances, etc.
							However, to open that padlock you need the private key
						Private Key
							One want want one copy of a private key because you can open all of the public keys with it
		
		Login via ssh
			ssh PUBLIC_IP_OF_INSTANCE -l ec2-user -i PRIVATE_KEY_FILE
				The user name is ec2-user for amazon's linux AMI

				If you're on a different AMI, simply remove the -l switch and you will get the correct username to log in from
	
	Security Groups
		A firewall that allows you to set inbound and outbound rules for different ports/protocols

		By default all inbound traffic is restricted and all outbound traffic is allowed

	EBS
		Useful linux commands
			If you set up an EBS and attach it to an instance, you need to do these steps
				lsblk - See file system and mount points
					Thus you can see all attached EBS volumes
				file -s PATH_TO_VOLUME
					PATH is usually /dev/NAME_GIVEN_IN_LSBLK
					If returned value is "data", then that means we don't have a formatted file system.  It's just raw.
						If the name of the file system is returned, we skip the mkfs and simply mount the drive
				mkfs -t ext4 /dev/NAME_GIVEN_IN_LSBLK
					make file system
					-t type
				mkdir MOUNTPOINT_DIR
				mount /dev/NAME_GIVEN_IN_LSBLK MOUNTPOINT_DIR
			If you need to unmount the volume
				umount /dev/NAME_GIVEN_IN_LSBLK

					If you go into the mountpoint directory, it is still there, but any attached files by the volume wont be present
				You can now go into the actions menu and detach the volume
		Volumes (from EC2 console view)
			Volume Type
				gp2 - General Purpose SSD drive
				standard - magnetic
		Once you've created a volume, make sure to attach it to a specific ec2 instance under the actions menu
			Snapshots
				After creating an volume, you can create a snapshot for backup purposes
				Another use case is you can create you whole dev/QA ENV on magnetic storage, and when you are read to transition QA over to PROD, you can take a snapshot of the volume and then create a volume from the snapshot (and set the storage type to SSD for example)

	Scenarios
		You aren't using RDS and you want to run your db on an EC2 instance.  You aren't getting the disk I/O that you want, what do you do? 
			A: Add more EBS volumes to the instance and put them in a RAID configuration in the given OS
	Creating an AMI
		Create a snapshot of a volume
		Go into snapshot tab and select the snapshot
		Actions tab, select "Create Image"

		The resulting AMI will be stored under the images tab
			By default the AMI is private
			Go back into actions tab and modify image permissions to make it public (if needed)
				You can also share it with a specific AWS account
			If sharing an AMI, AWS has an in-depth document for securely doing this.
				Topics include shreding SSH keys (public keys and private keys)
				Erasing bash history, etc.
	Load Balancing (LB)
		Client --> LB --> Servers

		Note: You can use this LB created here to load balance into an Auto Scaling Group (which is generally touched upon below
			)

		Create a LB in EC2 console
			Define the mappings of ports on the LB to ports on EC2 instances (they dont have to be the same)

			You can initiate SSL connections from the client to the LB
				You would need to set up the SSL certificate in this configuration step
			
			Step 2 (Configure Health Check)
				Set the ping path to index.html (or a small healthcheck text file)
			Step 4 - Add EC2 Instances
				Enable Cross-Zone Load Balancing
					What availability zones could be balanced.  
					If unchecked, only the current availability zone.
					Checked: Can balance into all availability zones across the overarching region that it's created in.
						You would want this checked because it's a good idea to spread ec2's across AZs 
	Bootstrap scripts
		Run at the root level right when the instance goes live
		Ex:
			#!/bin/bash <-- Needed for script to run
			yum install httpd -y
			yum update -y
			aws s3 cp --recursive s3://acloudguru/ /var/www/html
			chkconfig httpd on <-- apache will start automatically from now on
			service httpd start

	Auto Scaling
		Launch Configurations
			Used by auto scaling to configure how many instances you want when you utilize an auto scaling event

			All of the same prompts when spinning up an EC2 instance

		Create Auto scaling group
			You proceed to this step after you finish the launch configuration
			You can utilize the health checks of an LB (seen in this file) to spin up additional ec2 instances in the auto scale group

			Step 1 - Configure Auto Scaling group details
				Subnet
					This includes the availability zones that will be auto-scaled into.  The point of auto-scaling is to give you fault tolerance, so if you have 2 instances, select 2 availability zones (AWS will try to spread the load across AZ as much as possible).
						By default, go ahead and select every AZ possible
			Step 2 - Configure scaling policies
				When a certain alarm is triggered, you can increase/decrease the ec2 group size.
	
	Placement Groups
		Only related to EC2s
		A placement group is a logical grouping of instances with a single AZ.  Using placement groups enbables applications to participate in a low-latency, 10 Gbps network.  Placement groups are recommended for applications that benefit from low latency, high network throughput, or both.
			You can't have a placement group across multiple AZs
			Helps with really good network throughput from one EC2 instance to another
			The name you specify for a placement group must be unique within your AWS account
			Only certain types of instances can be launched in a place group
				Compute Optimized
				GPU
				Memory Optimized
				Storage Optimized
				Amazon recommends using the same instance type (and size) for all instances within the placement group
				You can't merge placement groups

				Youc an't move an existing instance into a placement group.  You can create an AMI from your existing instance, and then launch a new instance from the AMI into a placement group
