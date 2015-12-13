Key terms
	AZ - Availability Zone
	instance type
		determines the hardware of the computer used for your instance

Additional items to do for exam


General Tips/Info:
	Use DNS names whenever possible, AWS can switch IPs

	The URL in which to access instance metadata
		curl http://169.254.169.254/latest/meta-data/

		Get public IP
			curl http://169.254.169.254/latest/meta-data/public-ipv4
	
	Best Practices
		Remember to regularly patch the OS and all applications inside

		Use separate EBS volumes for the operating system vs your data

		Only use the instance store for temporary data.

		Use instance metadata and custom resource tags to track resources.

		Regularly back up your instance using EBS snapshots.

		Deploy critical components of you app across multiple AZ and replicate your data appropriately.

		Design your apps to handle dynamic IP addressing when your instance starts.
			EX: http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-instance-addressing.html

		Setup appropriate monitoring
			http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/monitoring_ec2.html

		Ensure that you can handle failover.
			Basic EX: Manually attach network interface or Elastic IP address to a replacement instance.
				OR, you can utilize an auto scaling.

			Regularly test the process of recovering your instances and EBS volumes if they fail.


	Helpful linux commands
		service httpd status
			check the status of apache

		service httpd start
			start apache

		chkconfig httpd on
			whenver machine restarts, it will start apache

		A full instance hour will be charged for every transition from a stopped state to a running state.
			Even if this occurs multiple times WITHIN the same hour.
			EX: An instance hour is $.10.  If you left the instance on for an hour, you would be charged $.10.  If you stopped and restarted that instance twice during that hour, you would be charged $.30 for that hour of usage. (initial $.10 for the start, then 2 x $.10 for the subsequent starts

EC2
	Connecting
		utilize the public DNS name
			This can change if the instance is stopped

		Connect via browser
			You can connect via the browser in the AWS Console.
			This will automatically input the IP and you can even cache the location of your pem file (on the HD).
			User-name will be ec2-user for AWS AMI Image

		Connect via mac/linux
			Once you download the .pem file make sure to set the permissions so only you can read it
				chmod 400 PEM_FILE.pem

			$  ssh -i /path/my-key-pair.pem ec2-user@public_dns_name


	Security Groups
		Ex name: IAM_USERNAME_SG_REGION_IDENT
		Act as a firewall for associated instances

		If launching an EC2 instance in a VPC, you can utilize the VPC console to set up the security groups

		If you plan to launch instances in multiple regions, you'll need to create a security group in each region.
			In general, make sure the security group is in the same region as your key-pair

		CIDR notation for only 1 IP:
			IP/32


	Key Pairs
		AWS uses public-key cryptography to secure the login information for your instance.
			A linux instance has no password; you use a key pair to log in to your instance via ssh.

			If you plan to launch the ec2 instance in multiple regions, you'll need to create a key-pair for each region.

			Suggested naming conventions:
				IAM_USER_NAME-key-pair-REGION_NAME

			
	Regions/AZ
		You can migrate an instance from one AZ to another

		Whenever you are in the AWS console, you'll only see resources for a given REGION

		All communications between regions is across the public internet.  Thus you should use appropriate encryption
			EC2 cross-region data transfer
				Data transfer between regions is charged at the Internet data transfer rate for both the sending and receiving instance


		EC2 resources are either global, tied to a region, or tied to a AZ.

		Each region is completely independent
			If you want to replicate across regions, you need to do this yourself
		AZ's are physically isolated, but the AZ in a region are connected through low latency links.


	Ways to access
		AWS CLI
			Provides commands for a broad set of AWS products
			Support on Windows, Mac, Linux
		
		AWS EC2 CLI
			Commands for EC2, EBS, VPC
			Supprt on Windows, Mac, Linux

		Language specific SDK
			Provide basic functions that automate tasks such as cryptographically signing your requests, retrying the requests, error handling.

		You can also interact/configure EC2 instances remotely via the Query API.
			HTTP/HTTPS requests that use GET/POST

			For client to EC2 communication



	
	Can create through
		Autoscaling group
		CloudFormation
		Elastic Beanstalk
		OpsWorks
		Manually

	Security
		CloudTrail
			Monitoring the calls made to EC2 API for your account (and calls made by the AWS Console), CLI, etc.

		Use IAM to control access to your AWS resources, including your instances.

		Disable password-based logins for instances launched from your AMI.


	Compliance
		EC2 is compliant with Payment Card Industry (PCI) Data Security Standard (DSS)

		To see all compliant AWS services
			http://aws.amazon.com/compliance/pci-dss-level-1-faqs/

	Elastic IP Addresses
		Static IPs for dynamic cloud computing

		Can be utilized to rapidly mask the failure of an instance in one AZ by remapping the address to an instance in another AZ.

	Tags
		Metadata for an instance

	Instance Pricing
		On Demand Instances
			Fixed hourly pricing

		Reserved Instances
			1 year or 3 year terms.

			There's a significant discount on the hourly rate for these types of commitments
			Can pay upfront or as you go

			Ex: You can have the reserved servers as a base and utilize the on-demand servers for scaling groups

		Spot Instances
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
		Local Instance Storage aka Instance Store Volume
			non-persistent
		
		Elastic Block Store (EBS)
			persistent

			Useful linux commands
					An AWS EBS volume serves as network-attached storage

					View mounted volumes
						df -h

						EX:
						Filesystem             Size   Used  Avail Use% Mounted on
						/dev/xvda1             8.0G   1.1G   6.9G  14% /
						tmpfs                  298M      0   298M   0% /dev/shm

						The root device contains the image used to boot the instance.  There's also room to install additional software, etc.

					In EC2 navigation plane, under EBS, click Volumes.
						Select the same AZ that you used when you created your instance.
					
					Create the volume

					Find the volume in the console and select 'Attach Volume'




				If you set up an EBS and attach it to an instance, you need to do these steps
					NOTE: Sometimes when you create a volume from a snapshot, the data on the volume is contained in a partition (such as /dev/xvdf1) instead of the root of the volume. In such a case, you would mount the /dev/xvdf1 partition

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

					You can now go into the actions menu and detach the volume

				If you go into the mountpoint directory, it is still there, but any attached files by the volume wont be present

			Volumes (from EC2 console view)
				Volume Type
					gp2 - General Purpose SSD drive
					standard - magnetic
			
				Once you've created a volume, make sure to attach it to a specific ec2 instance under the actions menu
				
				Snapshots
					After creating a volume, you can create a snapshot for backup purposes
					
					Another use case: You can create your whole dev/QA ENV on magnetic storage, and when you are ready to transition QA over to PROD, you can take a snapshot of the volume and then create a volume from the snapshot (and set the storage type to SSD for example)


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
		If creating an AMI
			You need to look into
				Which flavors of the Amazon Linux AMI are recommended on each Amazon EC2 instance type. (http://aws.amazon.com/amazon-linux-ami/instance-type-matrix/)

		Linux AMIs use one of two types of virtualization
			paravirtual (PV)

			hardware virtual machine (HVM)
				Recommended for the best performance.


			Main differences between the above types
				The way in which they boot and whether they can take advantage of special hardware extensions (CPU, network, and storage) for better performance.



		General Tips
			You can utilize a public AMI, customize it and then create your own AMI from that.

		An AMI includes the following
			A template for the root volume for the instance
				EX: OS, App Servers, Applications
			
			Launch permissions that control which AWS accounts can use the AMI to launch instances

			Block device mapping that specifies the volumes to attach to the instance when it's launched

		Must select an AMI that's in the same region as the instance
			There are ways to copy the AMI to the region that you're using.
		
		Categories
			Items across both categories
				When an instance is launched, the image that's used to boot the instance is copied to the root volume (typically sda1).  There is typically additional space on the root volume for adding software,etc.

			backed by Amazon EBS
				root device for an instance launched from the AMI is an EBS volume which is created from an EBS snapshot.

				The root (and any EBS volumes that you attach additionally) will be housed in the same AZ as your instance.

				AMI's backed by EBS are the recommended path because they launch faster and use persistent storage.

				these ami's automatically have an EBS volume attached

				When launching an EBS-backed instance, an EBS volume is created for each EBS snapshot that's referenced by the AMI that you use.  You can optionally use other EBS volumes or instance store volumes as well.

				Stopped state
						All EBS volumes remain attached

						In this state, you can attach or detach EBS volumes, create an AMI, change the kernel, RAM, instance type, change the size of the instance, attach your root volume to a different running instance for debugging, etc.

				Terminated state
					By default, the root device volume and the other EBS volumes attached during LAUNCH (aka from AMI, others attached during config) are automatically deleted when the instance terminates.

					By default, any EBS volumes that you attach to a RUNNING instance are detached with their data intact on termination.  You can attach and detached volume to any running instance.

				EBS backed instance failure
					Stop and then start again (try this first)

					Automatically snapshot all relevant volumes and create a new AMI.
						http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/RootDeviceStorage.html





			backed by instance store
				root device for an instance launched from the AMI is an instance store volume created from a template store in S3.

				Instances that use this setup automatically have instance store volumes available, with one serving as the root device volume.

				These types of instances don't support the "stop" action, only terminate.

				Data is deleted if the instance fails.

				Instance attributes are fixed for the life of an instance
					EX: You can't change the instance type, kernel, RAM disk, and user data, etc.  This is because you can't STOP the instance to make the changes.

					Cost benefits vs EBS backed instances
						Instance Store Backed
							You're charged for instance usage and storing your AMI in S3

						EBS Backed
							You're charged for instance usage, EBS volume usage, and storing your AMI as an EBS snapshot



		Amazon Linux AMI
			Comes with a lot of items, including the AWS command line.  Very helpful because you don't have to install the CLI every time you boot up a new instance

	Configuring an instance
		Steps (in AWS console)
			3 - Configure Instance
				Subnet - Is the availability zone for the instance
					Utilize the default
						This will match based on system health and available capacity.
							Only specify a custom when you need to logically isolate from a previous instance (fault tolerance)

				Shutdown behavior (Stop or Terminate)
					When you shutdown the instance, AWS can stop it (and the instance will persist) or terminate (the instance is deleted)

					If you're doing a 'shutdown -h' in linux
						State defaults to
							'stop' 
								if using an EBS volume for root
							'terminate'
								if using an instance-store root device

					You will not be charged when instances are in these states.

					Terminated state
						

						The instance itself is deleted.



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

		By default, all inbound traffic is restricted and all outbound traffic is allowed

		
	Scenarios
		You aren't using RDS and you want to run your db on an EC2 instance.  You aren't getting the disk I/O that you want, what do you do? 
			A: Add more EBS volumes to the instance and put them in a RAID configuration in the given OS
	
	Creating an AMI
		'/' contains the AMI used to boot the instance

		Create a snapshot of a volume
		Go into the snapshot tab and select the snapshot
		Actions tab, select "Create Image"

		The resulting AMI will be stored under the images tab
			By default the AMI is private
			Go back into the actions tab and modify the image permissions to make it public (if needed)
				You can also share it with a specific AWS account
			
			If sharing an AMI, AWS has an in-depth document for securely doing this.
				Topics include shredding SSH keys (public keys and private keys)
				Erasing bash history, etc.


	Load Balancing (LB)
		Client --> LB --> Servers

		Note: You can use the LB created here to load balance into an Auto Scaling Group (which is generally touched upon below)

		Create a LB in EC2 console
			Define the mappings of ports on the LB to ports on EC2 instances (they dont have to be the same)

			You can initiate SSL connections from the client to the LB
				You would need to set up the SSL certificate in this configuration step
			
			Step 2 (Configure Health Check)
				Set the ping path to index.html (or a small healthcheck text file)
			
			Step 4 - Add EC2 Instances
				Enable Cross-Zone Load Balancing
					I.e.: What availability zones should be balanced.  
					
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

		Create auto scaling group
			You proceed to this step after you finish the launch configuration
			
			You can utilize the health checks on an LB (seen in this file) to spin up additional ec2 instances in the auto scale group

			Step 1 - Configure Auto Scaling group details
				Subnet
					This includes the availability zones that will be auto-scaled into.  The point of auto-scaling is to give you fault tolerance, so if you have 2 instances, select 2 availability zones (AWS will try to spread the load across AZ as much as possible).
						By default, go ahead and select every AZ possible
			Step 2 - Configure scaling policies
				When a certain alarm is triggered, you can increase/decrease the ec2 group size.

	
	Placement Groups
		Only related to EC2s
		
		A placement group is a logical grouping of instances with a single AZ.  Using placement groups enables applications to participate in a low-latency, 10 Gbps network.  Placement groups are recommended for applications that benefit from low latency, high network throughput, or both.
			You can't have a placement group across multiple AZs
		
			Helps with really good network throughput from one EC2 instance to another
		
			The name you specify for a placement group must be unique within your AWS account
		
			Only certain types of instances can be launched in a placement group
				Compute Optimized
				GPU
				Memory Optimized
				Storage Optimized

				Amazon recommends using the same instance type (and size) for all instances within the placement group
				
				You can't merge placement groups

				You can't move an existing instance into a placement group.  You can create an AMI from your existing instance, and then launch a new instance from the AMI into a placement group
