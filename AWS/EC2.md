Key terms
	AZ - Availability Zone
	instance type
		determines the hardware of the computer used for your instance

	AL - Amazon Linux

	NAT - Network Address Translation
		Maps a public IP to a private IP

Additional items to do for exam


General Tips/Info:
	A subnet is NOT an AZ.  
		However, in a default VPC, there are 3 different subnets that are spread across 3 AZs

	If you create an AMI from an instance, that instance WILL be rebooted (unless no reboot is checked)
	Instances that access other instances through their public NAP IP are charged for regional or Internet data transfer, depending on whether the instances are in the same region.
		For cost effectiveness, look at ways around this.
			Launching into all of those instances into a VPC?
	Instances that access other instances through 
	Use DNS names whenever possible, AWS can switch IPs

	The URL in which to access instance metadata
		curl http://169.254.169.254/latest/meta-data/

		Get public IP
			curl http://169.254.169.254/latest/meta-data/public-ipv4
	
	Best Practices
		Create a key-pair for each user on a single instance
			Don't give all users the same key-pair

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
	Security
		Security Groups
			CIDR
				If you use the /0 prefix after the IP address, this opens the given port (in a security group setting, etc.) to everyone.

				If you're specifying a SINGLE IP for access, utilize the /32 prefix after the IP.
			A virtual firewall that controls the traffic for one or more instances

			Rules
				In the source field you can specify
					1 IP address

					IP address range

					The name of a security group
						This allows instances associated with the security groups to access eacthother

						This doesn't copy the security rules from one group to another

						You can specify one of the following security groups
							The current security group
								Thus there is a rule in the security group that references itself.
									This will allow traffic from other instances associated with the security group.
							If EC2-VPC
								A different security group for the same VPC

						You can make all instances in a security group talk to eachother (by assigning all of them to the same security group), or instances from different security groups talk to eachother.
							To make this happen, in the inbound rules allow
								ALL ICMP
								ALL TCP
								ALL UDP
								
								Source: The security group that's associated with the instance/s that you want to link to.


				If there are 2 rules for the same port, AWS uses the one that is most permissive (aka "open")


				Are always permissive; you can't create rules that deny traffic
					If a rule for a particular protocol isn't set, the traffic isn't allowed.

				Security groups are stateful
					If you send a request from your instance, the response traffic for that request is allowed to flow in regardless of inbound security group rules.

					In a VPC, this also means that responses to allowed inbound traffic are allowed to flow out, regardless of outbound rules.

					If your instance (host A) initiates traffic to host B and uses a protocol other than TCP, UDP, or ICMP, your instance’s firewall only tracks the IP address and protocol number for the purpose of allowing response traffic from host B. If host B initiates traffic to your instance in a separate request within 600 seconds of the original request or response, your instance accepts it regardless of inbound security group rules, because it’s regarded as response traffic. For VPC security groups, you can control this by modifying your security group’s outbound rules to permit only certain types of outbound traffic. Alternatively, you can use a network ACL for your subnet — network ACLs are stateless and therefore do not automatically allow response traffic. For more information, see Network ACLs in the Amazon VPC User Guide.

					If you want to ensure that traffic is immediately interrupted when you remove a security group rule, you can use a network ACL for your subnet — network ACLs are stateless and therefore do not automatically allow response traffic. For more information, see Network ACLs in the Amazon VPC User Guide.


			Are associated with network interfaces
				Changing an instance's security groups changes the security groups associated with the primary network interface (eth0).

				You can change the security groups associated with any other network interface.

			You can add multiple security groups to an instance

			If you're using a VPC, you must use security groups created specifically for your VPC.
				Are you launch an instance in a VPC, you can change its security groups

			You can update a security group at any time, and all associated instances are automatically updated.


		Fingerprinting
			You can utilize the fingerprint that's displayed in EC2's Key Pairs page to verify that the private key you have on your local machine matches the public key that's stored in AWS.

		Deleting A Key Pair
			You can delete a key pair using the ec2 console

			In this process, you are only deleting EC2's copy of the public key.

			This only revokes access to instances that haven't been launched.  

			If you're using an Auto Scaling group (ex: Elastic Beanstalk), ensure that the key pair that you're deleting is not specified in your launch config.
				In the event of an emergency, the instance launch fails of the key pair can't be found.

	Elastic IP Address
		A static IP that's associated with your AWS account (not a particular instance)
			This means that you can assign to whatever instance you want, and utilize it for failover situations

	Instance Lifecycle
		1) Pending
			When you launch, the instance enters the pending state
		2) Running
			Start to get billed for each hour or partial hour that you keep the instance running.
				At this point, you're guaranteed
					If launched into a VPC, the private IP address is given from the range of the subnet (if you don't specify one yourself).  For the primary private IP, this is linked to eth0.
						For instances launched in a VPC, the private IP remains associated with the network interface when the instance is stopped/restarted.  It is released when the instance is terminated.
							Aside: You can associate multiple private IPs with an instance
								On an instance, you can have multiple private IPs, with each one corresponding to an elastic IP.

								Use cases
									Host multiple websites on a single server by using multiple SSL certificates on a single server and associating each certificate with a specific IP address.

									Operate network appliances, such as firewalls or load balancers, that have multiple private IP addresses for each network interface

									Direct internal traffic to a standby instance in case another instance fails, by reassigning the secondary private IP address to the standby instance.
		3) Stop/Start (In EBS-backed instances)
			If the instance is running in Ec2-VPC, it retains its private IP address (thus any associated Elastic IPs still map correctly, etc.)  
				Remember that the instance DOES get a new public IP, unless it has an Elastic IP addr (which doesnt change).

			Each time you transition an instance from stopped to running, a full instance hour is charged



			Instance Reboot
				It's recommended you use EC2 interface to reboot your instance instead of running the OS command.

				Rebooting an instance is equiv. to rebooting an operating system.  The instance reamins on the same host computer and maintains its public DNS name/IP, private IP address, and any data on its instance store volumes.

				Rebooting an instance doesn't start a new instance billing hour.

			Termination
				Each EBS volume supports the DeleteOnTermination attr, which controls whether the volume is deleted or preserved when you terminate the instance it's attached to.
					The default is to delete the root device volume and preserve any other EBS volumes.

				OS shutdown commands always terminate an instance store-backed instance



	Instance Types



		T2
			Moderate baseline performance and the capability to burst to significantly higher performance as required by your workload.

			Intended for workloads that don't use the full CPU consistently, but occasionally need a "boost"

			Exs: web servers, dev envs, small dbs.

			Part of free tier

			Can't be a
				Spot Instance 
				Dedicated Instance
			
			CPU credits
				These are for T2 instances because they are the only type that can "burst"
					Traditional EC2 types provide fixed performance, but T2 instances provide a baseline level of CPU performance with the ability to burst above that level. 
				
				One CPU credit is equal to one vCPU running at 100% utilization for one minute.

				Other combinations of vCPUs, utilization, and time are also equal to one CPU credit.
					Ex: 1 vCPU running at 50% utilization for 2 mins OR
							2 vCPUs (on t2.medium and t2.large, for ex) running at 25% utilization for two mins.

				How CPU credits are earned
					http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/t2-instances.html
						Look at the credits earned table, etc.

					Each T2 instance starts with a CPU credit balance and then continuously recieves a set rate of CPU credits per hour, depending on the instance size.

					When a T2 instance uses fewer CPU resources than its base performance allows (i.e. it's idling, etc.), the unused CPU credits (or the net between what was earned/spent) is stored in a credit balance for up to 24 hours.  This "built" credits will allow for bursting.

					When the T2 instance requires more CPU resources than its base performance allows, it uses credits from the CPU credit balance to burst up to 100% utilization.
						The more credits your T2 instance has, the more time it can burst beyond its base performance level when more performance is needed.

				CPU credits expiring
					Initial CPU credits don't expire, but they are used first when an instance uses CPU credits.

					The non-initial earned credits will expire in 24 hours

					The CPU credit balance for an instance does not persist between instance stops/starts
						Once the instance restarts, it will receive its initial credit balance again.

				Credit Example
					For example, if a t2.small instance had a CPU utilization of 5% for the hour, it would have used 3 CPU credits (5% of 60 minutes), but it would have earned 12 CPU credits during the hour, so the difference of 9 CPU credits would be added to the CPU credit balance. Any CPU credits in the balance that reached their 24 hour expiration date during that time (which could be as many as 12 credits if the instance was completely idle 24 hours ago) would also be removed from the balance. If the amount of credits expired is greater than those earned, the credit balance will go down; conversely, if the amount of credits expired is fewer than those earned, the credit balance will go up.

				What happens if you use all of the credits?
					Performance will remain at the baseline performance level.

					You can't buy more credits, you need to move to a larger T2 size or a fixed performance instance (M3,C3, etc.)

				You can see the credit balance in the CloudWatch console.

				"Burst times"
					CPU credits are spent more quickly than they are earned.

			C4 Instances
				Ideal for compute-bound applications that need high performance processors (think 'C'ompute)
					Ex:
							Batch processing workloads
							Media transcoding
							High-traffic web servers, massively multiplayer online (MMO) gaming servers, ad serving engines, etc.
							High performance computing

				Hardware specs
					Go to AWS for all instances for latest info.

				EBS-optimized by default
					Deliver dedicated block storage throughput to EBS ranging from 500 Mbps to 4,000 Mbps

					You can enable enhanced networking capabilities.
						Gives significantly higher packet per second (PPS) performance, lower latencies and overall higher network performance.

						Can cluster C4 instances in a placement group

						c4.8xlarge
							Ability to control processor C-states and P-states on Linux.
								C-states control the sleep levels that a core can enter when it's inactive

								P-states control the desired performance (in CPU frequency)

							Provides 36 vCPUs, which can cause some issues in some Linux OSs that have a vCPU limit of 32.
								It's strongly recommended that you use the latest AMIs
									Ex: AL AMI 2015.06(HVM)
					D2 Instances
						Designed for workloads that require high sequential read/write access to very large data sets on local storage.

						Ex:
							Massive parallel processing data warehouse.

							MapReduce/Hadoop distributed computing

							Log/data processing apps

						Primary data storage is HSS-based instance storage.

						EBS-optimized by default

						Can enable enhanced networking abilities

						Can cluster into a placement group

						d2.8xlarge
							Provide the ability to control process C-states and P-states on linux.

							Capable of providing up to 3.5 GB/s read performance and 3.1 GB/s write performance with a 2 MiB block size.

							Has 36 vCPUs, so make sure your kernel supports it.

						Can utilize up to 244 GiB of RAM.




					I2 Instances
						Optimized to deliver tens of thousands of low-latency, random I/O operations per second (IOPS) to applications

						Good for
							NoSQL databases

							Clustered DB
							
							Online transaction processing systems

						Can enable enhanced networking abilities

						Can cluster in a placement group

						Can enable EBS-optimization to obtain additional, dedicated capacity for Amazon EBS I/O

						Primary data storage is SSD-based instance storage.
							Only lives for the life of the instance

						If you utilize the SSD-based instance store volumes available to the instance, you can get at least:
							Look at SSD I/O Performance table on http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/i2-instances.html

						As you fill the SSD-based instance storage on your instance, the number of write IOPS will decrease.

						Write Amplification
							Ratio of SSD write operations to user write operations

							Occurs from extra work the SSD controller must do to find space, rewrite existing data, and erase unused space so it can be rewritten.  This is collectively a garbage collection process

							To mitigate against this
								Over-provisioning
									Leave 10% of the volume unparitioned so that the SSD controller can use it for over-provisioning.  This decreases the storage that you can use, but increases performance.

									This isn't done by default

							I2 instance store-backed volumes support TRIM.
								You can utilize this command to notify the SSD controller whenever you no longer need data that you've written.

								This gives the controller more free space, which decreases write amplification.






					Linux GPU Instances
						For high parallel processing capability
							provide access to NVIDIA GPUs with up to 1536 CUDA cores and 4 GB of video memory.
								CUDA allows an API for CUDA enabled GPUs.  It is an architecture that allows for parallel computing of GPUs.

							Can use GPU instances to help with scientific, engineering, graphics apps/workloads, game streaming, 3-D app streaming and rendering apps by leveraging CUDA or OpenCL parallel computing frameworks.

						Can cluster into a placement group

						These instances can't access the GPS unless the NVIDIA drivers are installed
							NVIDIA provides AMIs for GPU utilization (includes drivers, etc.)



						





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

			Elastic Network Interfaces (ENI)
				Is a virtual network interface that you can attach to an instance in a VPC.

				Each instance in a VPC has a default ENI (the primary network interface) that is assigned a private IP address from the IP address range of your VPC.
					You can't detach a primary network interface from an instance.  Although, you can create and attach additional ENI's.
						Multiple ENIs are useful when
							Create a management network.

							Use network and security appliances in your VPC.

							Create dual-homed instances with workloads/roles on distinct subnets.

							Create a low-budget, high-availability solution.

					Creating a Management Network
						(Currently AT)

						The secondary ENI on the instance handles public-facing traffic and the primary ENI handles back-end management traffic and is connected to a separate subnet in your VPC that has more restrictive access controls.
							Public facing interface
								May (may not) be behind a load balancer

								Has an associated security group that allows access to the server from the internet.

							Private Interface
								Has an associated security group allowing SSH access from an allowed range of IPs either from within the VPC or from the Internet, etc.

							Make a secondary private IP for incoming traffic on an ENI
								In the event of an instance failure, you can move the interface and/or secondary private IP address to a standby instance.

				An ENI can include the following attributes
					A primary private IP address

					One or more secondary private IP address

					One Elastic IP address per private IP address

					One public IP address, which can be auto-assigned to the elastic network interface for eth0 when you launch an instance, but only when you create an elastic network interface for eth0 instead of using an existing network interface.

					One or more security groups

					A MAC address

					A source/destination check flag

					You can create an ENI, attach it to an instance, detach it from an instance, and attach it to another instance.
						The attributes in the ENI are preserved as it moves

						When you move an ENI from one instance to another, network traffic is redirected to the new instance



	Tags
		Metadata for an instance

	Instance Pricing
		On Demand Instances
			Fixed hourly pricing

		

		Instance Purchasing Options
			Dedicated Hosts, Spot Instances, Reserved Instances
			
			Dedicated Hosts
				A physical server with EC2 instance capacity fully dedicated to your use.

				Difference between Dedicated Hosts and Dedicated Instances
					Can both be used to launch EC2 instances onto physical servers that are dedicated to your use.

					Dedicated instances
						Dedicated Instances are Amazon EC2 instances that run in a VPC on hardware that's dedicated to a single customer. Your Dedicated Instances are physically isolated at the host hardware level from your instances that aren't Dedicated Instances and from instances that belong to other AWS accounts.

					Dedicated Hosts
						You can use also use Dedicated Hosts to launch Amazon EC2 instances on physical servers that are dedicated for your use. Dedicated Hosts give you additional visibility and control over how instances are placed on a physical server, and you can reliably use the same physical server over time.

					There are no performance, security, or physical differences between Dedicated instances and dedicated hosts.  Although, dedicated hosts give you additional visibility and control over how instances are placed on a physical server

				Auto scaling groups are not supported

				Placement groups are not supported

				Configurations
					Dedicated hosts are configured to support a single instance type capacity.  The number of instances you can launch onto a Dedicated host depends on the instance type that the Dedicated host is configured to support.
						Ex: You allocated a c3.xlarge instance on a Dedicated host.  
							You have the right to launch up to 8 c3.xlarge instances on that host.

				Pricing and Billing
					An On-Demand pricing model.

			Reserved Instances
				1 year or 3 year terms.

				Is not a physical instance, it's a reserved price.

				When you purchase a Reserved instance, the reservation is automatically applied to running instances that match your specified parameters.

				No Upfront and Partial Upfront Reserved instances are billed for usage on an hourly basis, regardless of whether or not they are being used. All Upfront Reserved instances have no additional hourly charges.

				You can use Auto Scaling or other AWS services to launch the On-Demand instances that use your Reserved instance benefits.

				You can also sell your Reserved instance

				Modifying Reserved Instances
					You can
						Switch AZ within the same region
						Change between EC2-VPC and EC2-Classic
						Change the instance type within the same family


				There's a significant discount on the hourly rate for these types of commitments
				Can pay upfront or as you go

				Ex: You can have the reserved servers as a base and utilize the on-demand servers for scaling groups

			Spot Instances
				Only use this if your application has flexible start and end times

				You set a maximum price that you're willing to pay, and if the current price is under that, you get the instance.  However, if at any time the prices exceeds your maximum price, then your instance is lost.
					You need to be writing to something with durability (i.e. s3 bucket, etc)

				Look at certain times for more competitive rates
					Ex: Sunday morning between 2-4 am

				Good for data analysis, batch jobs, background processing, and optional tasks.
					Maybe utilize this with SQS, etc. because the spot instance can be pulled out at any time.

				Prices are adjusted on the hour

				Spot pool
					Set of unused EC2 instances with the same instance type, operating system, Availability Zone,and network platform (ex: Ec2-VPC)

				Spot price
					The current market price of a Spot instance per hour.
						This is set based on the last fulfilled bid.

					Everyone pays the same spot price, regardless of your bid

					At the start of each instance hour, you're billed based on the Spot Price.  If your Spot instance is interrupted in the middle of an instance hour because the Spot price exceeded your bid, you are not billed for the partial hour of use.
						If you manually terminate your instance at this time, you WILL be billed for the partial hour of use.

					Spot instances with a predefined duration use a fixed hourly price that remains in effect for the Spot instance while it runs.

				Spot instance request (Spot bid)
					The maximum price that you're willing to pay per hour for a Spot instance.

					A Spot Instance request is either one-time or persistent.
						Persistent
							Once the termination of an instance occurs, the spot instance request will be automatically resubmitted.

					The request can optionally specify a duration for the Spot instance.

				Spot fleet
					A set of Spot instances that is launched based on criteria that you specify.

					The fleet selects the Spot pools that meet your needs and launches Spot instances to meet your target capacity.

					The fleet also maintains the target capacity over time by launching replacement instances once other instances have been terminated.

				When a spot instance is about to be terminated, the instance is given a 2 minute notice.

				Ways of launching
					Via ec2

					Configure an auto scaling group (with a bid price) to launch spot instances

					With EMR

					Through a CloudFormation Template

					AWS SDK for Java/.Net
						You can utilize the Java/.Net programming language to manage your Spot Instances

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
			non-persistent, sometimes called ephemeral storage
		
		Elastic Block Store (EBS)
			Can be detached and reattached to other EC2 instances
			persistent
				Instance store volumes can't do this

			Instances with these volumes can be stopped, and the data will persist.
				If you do this with an EC2 instance with instance store drives, the data will be wiped on those drives

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
		Performing Updates
			Start a screen session incase you get disconnected

				$ screen

				If you get disconnected
					$ screen -ls

					Find the pid

					$ screen -r PID

				When finished
					$ exit

		 	$ sudo yum update

		 	Reboot the instance

		 	Updating a single package
		 		$ sudo yum update openssl




		Amazon Linux AMI
			Compiling from source
				$ yum groupinstall "Development Tools"
					Installs compilers, etc.

				If needed, decompress the tarball
					$ tar -xzf SOFTWARE.tar.gz

					Look at README or INSTALL file in root

			Managing User Accounts in the instance
				Default system user account
					ec2-user

					Linux system users shouldn't be confused with IAM users

				Add users
					$ sudo adduser NEWUSER

					This adds an entry in /etc/passwd, creates a NEWUSER group, and creates a home directory in /home/NEWUSER

					Provide remote access to this account
						Must create a .ssh directory in the NEWUSER home directory and create a file within named "authorized_keys".  It is this file that will contain a public key.

						$ sudo su - NEWUSER
							Look for prompt change

						mkdir .ssh

						chmod 700 .ssh
							Owner can read/write/exec

						touch .ssh/authorized_keys

						chmod 600 .ssh/authorized_keys

						Add in a public key for the user that you created through the EC2 console
							Ex: ssh-rsa JIBBERISH

				To remove a user from the system
					$ sudo userdel -r OLD_USER
						-r deleted the user's home directory and mail spool




			Hostnames
				With each instance, there is a private DNS
					Structure
						ip-12-34-56-78.us-west-2.compute.internal

					This allows instances within a VPC to communicate with eachother

						compute = service

						Part of this hostname is displayed at the shell prompt when you log into you instance

					Each time you stop and restart a your VPC EC2 instance, these parts change
						Public IP
						Public DNS name
						system hostname
						shell prompt

				Changing the System Hostname
					If you have a public DNS name registered for the IP address of your instance (ex zach.com), you can set the system hostname so your instance identifies itself as a part of that domain.

					This name is internal to the instance, and is used for a variety of networking functions

					To change
						/etc/sysconfig/network
							HOSTNAME=ENTER_HOST_HERE.COM
						sudo reboot
						$ hostname
							used to verify

					Change the system hostname without a public DNS

						/etc/sysconfig/network
							HOSTNAME=DOMAIN_PREFIX_HERE.localdomain

						/etc/hosts
							Change the 127.0.0.1 entry
								127.0.0.1 DOMAIN_PREFIX_HERE.localdomain DOMAIN_PREFIX_HERE localhost localhost.localdomain

						sudo reboot
						$ hostname
							to verify

				Setting up dynamic DNS on your linux instance
					When you launch an EC2 instance, it's assigned a public IP and public DNS name

					As these names are quite long, etc. there is a "Dynamic DNS" service which provides custom DNS host names.  

					You can use a dynamic DNS provider with EC2 and configure the instance to update the IP address associated with a public DNS name each time the instance starts.
						Look at http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/dynamic-dns.html

						Note that this service might take a few minutes to update the DNS record, so it isn't ideal for failover, etc.


			User Data: Scripts and cloud-init directives
				User data is executed only at launch.  
					If you stop an instance, modify the user data, and start the instance, the new user data is not executed automatically.
						There is ways to modify the user data in the AWS console.


				You can pass scripts and cloud-init directives into the User data field when starting an EC2 instance via the console (step 6)

				Can be leveraged to run commands on startup, etc.

				Only run during the first boot cycle when an instance is launched

				User data scripts must start with #! characters and the path to the interpreter that you want to read the script (commonly /bin/bash)

				Scripts entered as user data are executed as root, so you don't need to use sudo.
					Any files that you create will be owned by root, so you must modify permissions if you need non-root access.

				Debugging
					/var/log/cloud-init.log
						Captures console output while your startup script is running, etc.

				To access user data inside the instance
					http://169.254.169.254/latest/user-data

				Ex of launching multiple Ec2 instances (with user data) and using a launch index to script off of what instance should get what user data
					Example: AMI Launch Index Value
						http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-metadata.html




			Security
				AL AMIs are configured to automatically install security updates at launch time.

			Package/dependency management
				Done through yum

				Launch with two repos enabled
					amzn-main
					amzn-updates

					Amazon Linux instances have the GPG keys and repository information for the EPEL repository installed by default
						Contains extra software

				Installing Software Packages from a repo that has been added (or already present)
					$ yum install PACKAGE

				Install RPM package files that have been downloaded from the internet
					$ yum install MY_PACKAGE.rpm


				Repo Management
					Adding a repo to /etc/yum.repos.d
						sudo yum-config-manager --add-repo https://www.test.com/repository.repo

					Enable a yum repo
						$ sudo yum-config-manager --enable EXAMPLE

					$ yum grouplist
						To list the groups that are already installed on your system
						(and the groups available for install)


						Yum also combines several packages itno groups that you can install with one command to perform a particular task (ex: installing a web server, etc.)

					To get more info about a group
						$ yum groupinfo "Group Name"

						This command lists all of the mandatory, default, and optional packages that can be installed with that group.

					Install a group of packages
						yum groupinstall "GROUP NAME"

						By default, this will only install the madatory and default packages in the group listing
							EX: To install more than this
								yum --setopt=group_package_types=mandatory,default,optional groupinstall "Performance Tools"




				Finding Software Packages
					$ yum search
						Allows you to search the descriptions of packages that are available for your configured repos.

						Multi word searches in quotation marks only return results that match the exact query.

				yum update -y
					Updates the packages.
					Rolls you from one version of AL to the next.

				AL is designed to be used with package repos hosted in each AWS ec2 region.
					These repos provide ongoing updates to packages in the AL AMI, as well as access to hundreds of additional common open source server apps.


					These repos are accessed using yum
						http://aws.amazon.com/amazon-linux-ami/2015.09-packages/
							A list of packages available for the .09 release
								Always check to see if a package is here first before installing yourself

							Ex: sudo yum install httpd

							Access to the Extra Packages for Enterprise Linux (EPEL) repo is configured, but isn't enabled by default.  EPEL provides third-party packages in addition to those that are in the AL repos.

				AL supports scp and sftp for uploading apps onto a live instance






				lock-on-launch
					Locks your newly launched instance to receive updates only from the specified release of the AMI.
						EX: You launch a 2015.03 AMI and lock-on-launch
							You will receive only the updates that were released prior to the 2015.09 AMI.

						You can do this to mitigate the risk for breaking changes

						To enable
							Launch an Ec2 instance with the following user data passed to cloud-init.
								Can pass info into cloud-init using the EC2 console or the ec2-run-instances -f

					You can also lock existing instances
						Edit /etc/yum.conf.
						Comment out releasever=latest
						Run "yum clean all" to clear the cache


			AWS provides ongoing security and maintenance updates to all instances running Amazon Linux

			Doesn't allow remote root SSH by default.
			Password authentication is disabled to prevent brute-force attacks

			Only account that can log in via ssh is "ec2-user"
				This account has sudo privileges

			image metadata
				cat /etc/image-id
								/system-release
								/system-release-cpe
									Machine readable file

			For instances launched using IAM roles, a simple script has been included to prepare AWS_CREDENTIAL_FILE, JAVA_HOME, AWS_PATH, PATH, and product-specific environment variables after a credential file has been installed to simplify the configuration of these tools.

			/opt/aws/bin
				sym links to /bin directories in each of the installed tools directories

			/opt/aws/{apitools|amitools}
				Products are installed in directories of the form NAME-VERSION and a symlink NAME that is attached to the most recently installed version.

			/opt/aws/{apitools|amitools}/NAME/environment.sh
				Used by files in /etc/profile.d/ to set product specific ENV variables.

			cloud-init
				An open source package for bootstrapping Linux images in a cloud ENV.
					You can specify actions that should happen to your instance at boot time.

					Amazon Linux uses cloud-init to perform initial config of the ec2-user

					More info
						http://cloudinit.readthedocs.org/en/latest/

					AL uses theses cloud-init actions(config via /etc/sysconfig/cloudinit)
						action: INIT (always runs)
							Sets a default locale
							
							Sets the hostname
							
							Parses and handles user data
						
						action: CONFIG_SSH
							Generates host private SSH keys
						
							Adds a user's public SSH keys to .ssh/authorized_keys for easy login and administration
						
						action: PACKAGE_SETUP
							Prepares yum repo
							Handles package actions defined in user data

							Your custom applications can be uploaded during the instance launch by using this action.

						action: RUNCMD
							Runs a shell command
						action: RUN_USER_SCRIPTS
							Executes user scripts found in user data
						action: CONFIG_MOUNTS
							Mounts ephemeral drives
						action: CONFIG_LOCALE
							Sets the locale in the locale configuration file according to user data

						Supported User-Data Formats
							NOTE: You can also specify the user-data (in general) under Launch EC2/Step 3 Configure Instance/Advanced Details

							There are ways to blend different formats by using MIME
								http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AmazonLinuxAMIBasics.html
							
							Gzip	
								Will be automatically decompressed

							Base64 decoding

							User-Data script
								Begins with "#!" or Content-Type: text/x-shellscript

								Script is executed by /etc/init.d/cloud-init-user-scripts
									Occurs during the first boot cycle and it occurs after the initial config actions are performed

							Include file
								Begins with #include or Content-Type: text/x-include-url

								Contains a list of URLs, one per line.
									Each of the URLs is read and loaded

								Content can be gzipped, MIME-multi-part, plain text.

							Cloud Config Data
								Begins with #cloud-config or Content-Type: text/cloud-config

							Cloud Boothook
								Begins with #cloud-boothook or Content-Type: text/cloud-boothook

								Earliest hook available.

								Note that there is no mechanism provided for running it only one time. The boothook must take care of this itself. It is provided with the instance ID in the environment variable INSTANCE_ID. Use this variable to provide a once-per-instance set of boothook data.


								/var/lib/cloud
									This is where the content is stored











		Deregister
			AMI/Actions/Deregister
				Note the ID if you're deleting the snapshot

			This will not delete the snapshot

		Copying an AMI
			Tips
				Make sure all of the configs for the transfered AMI are updated for the new region.  If not, cross-region performance problems/costs can occur.
			Benefits
				You can copy AMIs that you own to other AWS regions to help with fault-tolerance

				Consistent deployment
					Have a base AMI that you utilize

			Limits
				You can copy both EBS-Backed and instance store-backed AMIs

				You can copy to as many regions as you'd like

				Linux AMI with encrypted volumes
					You can't copy it using the console.
						But, you can manually copy the snapshot for each volume to the destination region.
							Then, register a new AMI in the destination region, specifying the snapshots in its block device mapping.


			Each copy of an AMI results in a new AMI with its own unique AMI ID

			The AMI is launched into the region that it's in.

		Creating you own EBS-Backed AMI from an Instance
			Launch an instance from an AMI that's similar to the AMI that you'd like to create (aka base AMI).

			Once you're finished with your customizations, stop the instance

			Create the image
				This is automatically registered by AWS for EBS-Backed AMIs

				The instance is automatically powered down before creating the AMI to ensure that everything on the instance is stopped and in a constant state during the creation process.
					If you're confident that you DON'T need this, you can override this default behavior (aka dont power down and reboot the instance).  
						Useful for "hot backups", etc.

					Some file systems, like xfs, can freeze and unfreeze activity
						This helps make it safe to create the image without rebooting the instance.

				During the creation process, AWS creates snapshots of your instance's root volume and any other EBS volumes attached to your instance.
					If any of these volumes are encrypted, the new AMI will only launch on instances that support EBS encryption.

				To speed up the creation process, it is recommended to create snapshots of your volumes immediately before creating the AMI.
					http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-creating-snapshot.html

				Once the process completes, you have a new AMI and snapshot created from the root volume of the instance.
					When the AMI is launched, AWS creates a new EBS volume for the root volume using the snapshot.

				If you add instance-store volumes or EBS volumes to your instance in addition to the root device, the block device mapping for the new AMI contains is updated with this information.
					Remember the instance-store volumes will not contain any information.

				When creating the image, you can modify
					The size of the root volume

				Remember: Don't delete the snapshot that's associated with the AMI

		Creating a Linux AMI from a Snapshot
			EC2 Console
				Snapshots (under EBS)
					Choose the snapshot and choose Actions/Create Image
						If utilizing a PV virtualization type
							You must specify a Kernel ID and RAM disk ID.
							If you choose the default Kernel ID, your instance may fail the health checks if it's incompatible with the instance.


		Security considerations
			If utilizing a non-amazon public AMI, you need to make sure that there isn't any pre-installed credentials that would allow unwanted access to your instance, or any pre-configured remote logging that could transmit sensitive data to a third party.  These are just a few examples.

			In general, check the documentation for the Linux distro used by the AMI for info about improving the security of the system.

			While doing the following sanitation/check, initiate 2 ssh sessions until you've removed credentials that you don't recognize and confirmed that you can still log into your instance via SSH.
				Find any unauthorized public SSH keys.  The only key in the file should be the key used to launch the AMI.
					sudo find / -name "authorized_keys" -print -exec cat {} \;
						http://unix.stackexchange.com/questions/12902/how-to-run-find-exec
				Disable password-based remote authentication for root.
					Open ssh_config and edit the PermitRootLogin
						PermitRootLogin without-password
							OR
						PermitRootLogin No
							To disable completely

				Disable Local Root Access
					sudo passwd -l root
						Double check this

						Does not impact sudo

				
				Check whether there are any other user accounts that are able to log in to your instnace.

				Check for open ports that you aren't using and running network services listening for incoming connections.

				Prevent preconfigured remote logging by deleting the existing configuration file and restart the rsyslog service

				Make sure that all cron jobs legit

				Run a yum update, etc. on a regular basis.  You need to make sure that this doesn't break any of your other software, etc.



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
			

			Step 1 - Configure Auto Scaling group details
				You can utilize the health checks on an LB (seen in this file) to spin up additional ec2 instances in the auto scale group
					Make sure that the health checks provision enough time for an EC2 instance to start up correctly.
						If not, it will try to start a new instance, then delete it, and do an infinite loop.

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

	Instance Metadata
		Great for scripting purposes.
			Ex: Get me the local IP and do something, etc.
		Dynamic data
			Exs: Instance identity document that is generated when the instance in launched

		You can access the user data that you supplied when launching your instance.

		You can only access instance metadata and user data from within the instance, but that data itself isn't protected by cryptography.
			Don't store sensitive data (passwords, etc.) in this way.

		To view all categories of instance metadata within an instance
			Remember if you are after an IP address, etc. you want the instance's METADATA not its user data.
			curl http://169.254.169.254/latest/meta-data/
				The gets the 'latest' version of the API for the metadata.  Go into the root directory to see all versions.  It might be smart to correlate this to a fixed version of the API.

				Remember IP addr for exam

	Amazon EC2 Run Command
		Enables you to remotely manage the configuration of your EC2 instances without having to locally login to the instance/s in question.

		You can simultaneously execute commands on multiple instances


		Exs:
			Run Shell scripts
			Add users or groups and configure permissions
			View all running services
			Stop/Start services
			View system resources
			View log files
			Perform file operations
			Install/Uninstall apps

		This command utilizes pre-defined EC2 Simple Systems Manager (SSM) documents.
			You determine what you want to do on an instance, and you select the pre-defined SSM document to perform the operation.
				Ex: AWS-RunShellScript doc to execute shell scripts on an instance

		Runs through the instance's given IAM role.
			Through IAM, you can control which commands a user/group of users can perform on one or more instances.

		Stores the command history for 30 days, and is also replicated into CloudTrail (persisted until deletion).

		You can send multiple commands at the same time, and they execute asynchronously.
			The order of command execution is not guaranteed.

		If the instance is unresponsive when the command is issued, it is placed into a queue (for up to 31 days).

	Importing/Exporting Instances
		You can use the AWS VM Import/Export tools to import VM images from your local ENV into AWS and convert them into AMIs or instances.  
			Then, you can export the VM images back to your local ENV.

			VM Import/Export is compatible with
				Citrix Xen
				Microsoft Hyper-V
				VMware vSphere

		Can be used to migrate apps and workloads, copy your VM image catalog, create a disaster recovery repo for VM images.

	Scheduling Events for your instances
		AWS can schedule events for your instances
			Ex: reboot, stop/start, retirement


	Monitoring
		Beginning Steps
			Establish a baseline for normal performance in the ENV.
				Ex: CPU/network utilization, disk I/O 

		At a minimum, you should monitor
			These will help gain a baseline as well, 
			These EC2 Metrics in CloudWatch
				CPUUtilization
				NetworkIn (for network utilization)
				NetworkOut
				DiskReadOps
				DiskWriteOps
				DiskReadBytes
				DiskWriteBytes
			Memory utilization
			Memory used
			Memory available
			Disk Swap utilization
			Swap used
			Disk Space utilization
			Disk Space used
			Disk Space available

		Automated Monitoring Tools
			Status Checks
				AWS performs automated checks on every running EC2 instance to identify hardware and software issues.

				Viewing Status checks(creating alarms based on the checks)
					EC2 Console 


				Are performed every minute and each returns a pass/fail status.  If all checks pass, the overall status of the instance is OK.  If one or more checks fail, the overall status is impaired.
					You can trigger alarms based off of these checks.

				Types of checks
					System Status Checks
					Instance Status Checks

				System Status Checks
					These might need AWS involvement to repair (or you can try starting/stopping your instance to 'reset')
					Exs:
						Loss of network connectivity
						Loss of system power
						Software issues on the physical host
						Hardware issues on the physical host

					We can create an alarm that automatically recovers an instance when these types of checks fails.
						The recover action is only supported on
							C3, C4, M3, M4, R3, and T2 instance types.
							
							Use EBS storage exclusively

							Instances with shared tenancy

					Remember that after a recovery, the instance retrieves a new public IP (if not using an EIP)

				Instance Status Checks
					Monitor the software and network configuration of your individual instance.

					These checks detect problems that require your involvement to repair.

					Exs of instance status check failures:
						Failed system status checks
						Misconfigured networking/startup config
						Exhausted memory
						Corrupted file system
						Incompatible kernel

			AWS CloudWatch Alarms
				Watch a single metric over a time period, and perform one or more actions based on the value of the metric.
					Action is a notification sent to an SNS topic or Auto Scaling policy.  

					Alarms invoke actions for sustained state changes only.  Alarms will not invoke actions simply because they are in a particular state, the state must have changed and been maintained for a specified number of periods.

			CloudWatch Logs
				Monitor, store, and access your log files from Ec2 instances, CloudTrail, or other sources.

			EC2 Monitoring Scripts
				Perl scripts that can monitor memory, disk, swap file usage in your instances.
					http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/mon-scripts.html

		Manual Monitoring Tools
			The Ec2 and CloudWatch console dashboards provide an at-a-glance view of the state of your EC2 env.

			Make sure to check the log files on your ec2 instances

		

-----------------------------------------------------------
    EC2
-----------------------------------------------------------
	Elastic Cloud Computing
	Virtual Machines in the cloud
	Webservice that provides resizable compute in the cloud. 
	Pay only for the capacity used.

	Types of EC2
	1. OnDemand: Low cost and Fixed. Only when needed. 
	2. Reserved: Base line capacity. Steady state or predictable usage. 1000 active users. If you know what you will need.  
	3. Spot: Supply and Demand. Like stock market. 
				When spot price goes higher it will be deleted. 
				Spot and bid price are same then it will be bought. Flexible start and end. 
				Urgent computing needs. 
				If spot instances are terminated by amazon, amazon pays for hour. 
				You bought for 1 and price goes to 1.5 then amazon can terminate it. 
				You terminate, you pay for the hour.
	4. DedicatedHost: Pay by the hour. Only hourly rate.
				Regulatory requirements. Seperate region. Great for licensing. 
				!It does not support multi tenancy or cloud deployments.  

	EC2 Family. 
	!DR MC GIFT PX# (pic->EC2Family.png)
	Dynamic, Memory Optimized, General purpose, CPU optimized
	Graphic intensive, IO optimized, Field Array, Low cost, Graphics, Memory optimized. 
-----------------------------------------------------------
    IOPS
-----------------------------------------------------------
	Input Output operations per second. 
	It is a performance measurement used to characterize the computer storage 
	devices like HDD, SSD, SAN(Storage Area Networks)
	IOPS are analogus to "Revolutions Per Minute" of an automoblie engine. 
	https://en.wikipedia.org/wiki/IOPS
	# IOPS * Transfer size in Bytes = Bytes per sec(MBps)#
-----------------------------------------------------------
    EBS
-----------------------------------------------------------
	Elastic Block Store
	Storage Volumes that you can attach to Virtual Machines. 
	Block based storage. You can install operating system, databases etc.
	(pic:blockstorage1.png, 2.png)
	Replicated in an Availability Zone. 
	Not replicated to different availablity zone.

	Volume Types:
	1. General Purpose SSD (GP2)
		 Ratio of 3 IOPS (pic:iops.png) per GB with up to 10,000 IOPS and the 
		 ability to burst up to 3000 IOPS for extended periods of time
		 for volumes up to 1Gib.
	2. Provisioned IOPS SSD (I01)
		Designed for I/O intensive applications such as large relational or non
		relational NoSQL databases. 
		Use only if you need more than 10000 IOPS. 
		Can provision up to 20000 IOPS. 
	3. Throughtput Optimized HDD (ST1)
		Magnetic Storage
		Big Data, Data ware houses, Log processing. 
		*Cannot* be boot volume. 
	4. Cold HDD (SC1)
		Low cost storage *Notbootable*
	5. Magnetic (Standard)
		Low cost and *bootable*

	!You cannot mount 1 EBS volume to multiple EC2 instances, instead use EFS

	EBS root volumes of your DEFAULT AMIs cannot be encrypted. 
	You can use third party tool such as bit locker etc to encrypt root volume. 
	Or this can be done when creating AMIs in the AWS console or using the API.
	Additional volumes can be encrypted.
	reference: 
	http://www.slideshare.net/AmazonWebServices/ebs-webinarfinal
-----------------------------------------------------------
    Security Groups
-----------------------------------------------------------
	Virtual Firewall 
	First line of defence
	!Any changes to security group, reflects immediately. 
	!Security Groups are stateful. If you add inbound rule, they apply to outbound rule also. 
	!Even if you delete outbound all option, based on inbound rules like http 80, it works. 

	!Important
	1. All Inbound traffic is blocked by default. 
	2. All Outbound traffic is allowed. 
	3. Changes to security group take effect immediately. 
	4. You can have any number of EC2 instances within a security group. 
	5. You can have multiple security groups attached to EC2 instances. 
	6. Security groups are stateful. 
		If you create an inbound rule allowing traffic in, that traffic is automatically 
		allowed back out again.
	7. You cannot block specific IP addresses using Security Groups, instead use 
		Network Access Control Lists. 
	8. You can specify allow rules, but not deny rules. 
-----------------------------------------------------------
    Volumes and Snapshots
-----------------------------------------------------------
	Volumes exist on EBS.
	Snapshots exist on S3. 
	!Snapshots are incremental, this means that only the blocks that have 
	!changed since your last snapshot are moved to S3. 
	If Snapshot is first time, it may take some time to create. 
	Volume Types:
	General Purpose (GP2), Provisioned IOPS (IO1), Cold HDD(sc1),
	Throughput Optimized HDD(st1), Magnetic
	!Root volume is volume where operating system is installed.
	Steps to create volume and attach volume
	[bash]
		#ssh in to EC2 instance. 
		ssh ec2-user@EC2 -i publikey.pem
			yes
		sudo su
		lsblk
			xvda->xvda1
			xvdf->not attached
		cd /
		mkdir myfileserver
		file -s /dev/xvdf
			data(no data)
		mkfs -t ext4 /dev/xvdf #make file system
		mount /dev/xvdf /myfileserver
		cd /myfileserver/
		ls
			lost+found
		nano index.html
			<html><h1>Hello</h1></html>
			press Ctrl+x and save
		ls
			index.html
		echo "hello" > hello.txt
		ls
			hello.txt index.html lost+found
		lsblk
			xvda
			xvdf /myfileserver
		umount /dev/xvdf

		#Actions detach volume (if you dont unmout you will get error)
	[end]

		Create Snaphsot
		Snapshots are point in time photo graphs of virtual hard disks
		Everytime you take a snapshot, it saves only incremental updates.
		Once Snapshot is created, you can create volume from it. 
		Now when volume is being created, you can change the volume type to a new type.
		It can be gp2 or io1 or sc1 or st1 or magnetic
-----------------------------------------------------------
    RAID, Volumes & Snapshots
-----------------------------------------------------------
	RAID = Redundant Array of Independent Disks. 
		RAID 0 - Striped, No Redundancy, Good Performance. 
		RAID 1 - Mirrored, Redundancy. 
		RAID 5 - Good for reads, bad for writes, AWS does not 
			recommend RAID 5 on EBS. 
		RAID 10 - Striped & Mirrored, Good redundancy, Good performance. 
		(pic:raid.jpg,raid10.jpg)
	!Either RAID 0 or RAID 10
	How can i take snapshot of RAID Array?
		1. Stop the application from writing to disk.
		2. Flush all caches to the disk. 
		3. Freeze the file system. 
		4. Unmount the RAID Array. 
		5. Shutdown the associated EC2 instance. 
	Snapshots of encrypted volumes are encrypted automatically. 
	Volumes restored from encrypted snapshots are encrypted automatically. 
	You can share snapshots, but only if they are unecrypted. 
	These snapshots can be shared with other AWS accounts or made public. 
	
	AMI can be selected based on:
	1. Region and Availability Zone. 
	2. Operating System. 
	3. Architecture (32 bit or 64 bit).
	4. Launch permissions. 
	5. Storage for Root Device. 

	Different AMI Storage Types (Root Device Volumes)
	1. EBS Backed Volume. 
	2. Instance Store (Ephemeral storage)

	!With Instance Store, you cannot stop the EC2 instance. 
	With EBS Backed Volume, you can stop the EC2 instance.

	#EBS vs Instance Store#
	All AMIs are categorized as either backed by Amazon EBS or backed by
	Instance Store.
	(pic:EBSvsEphemeral.png)
	https://www.youtube.com/watch?v=J2cPYF6tkPE
-----------------------------------------------------------
    Elastic Load Balancer
-----------------------------------------------------------
	How to create Load Balancer. 
	>Login in to EC2
	[bash]
	sudo su
	ls
		index.html
	nano healthecheck.html
		Hello, this instance is healthy. 
	ls
	[end]
	!You can assign multiple secrity groups to load balancer.
	Elastic Load Balancer do not have public IP address. 
	ELB state can be In Service or Out of Service. 
	Health checks are performed to see it is working or not. 
	Have their own DNS name. You are never given an IP address. 
-----------------------------------------------------------
    CloudWatch
-----------------------------------------------------------
	Standard monitoring is turned on by default. *five* minutes.
	Detailed monitoring you have to subscribe to. *one* ninute. 
	Can create Dashboards.
	Can create alarms and take actions. 
	Events - allows you to respond to state changes. Trigger a lambda function.
	Logs - Cloudwatch logs helps you to aggregate, monitor and store logs.  
-----------------------------------------------------------
    AWS Command Line
-----------------------------------------------------------
	!IAM role can not be assigned after EC2 instance is started. 
	(pic:IAM-role-after-ec2.png)

	[bash]
	ssh ec2-user@EC2 -i KeyPair.pem
	sudo su
	clear
	#use command line. s3 buckets
	aws s3 ls
	aws configure
		AWS Access Key ID: .
		AWS Secret Key: .
		Default Region: us-west-1
	clear
	aws s3 ls
		all buckets: global
	aws s3 help
	#exit out of help using Ctrl+c
	#check where credentials are stored
	cd ~
	ls
	cd .aws
	ls
		config credentials
	nano credentials
		aws_access_key_id = .
		aws_secret_access_key = .
	#storing credentials on the machine is not a good thing. 
	#thats where roles come in to picture. 
	[end]
-----------------------------------------------------------
    Identity Access Management Roles 
-----------------------------------------------------------
	
	This time, we will create a role s3-full-access and assign the role
	to new EC2 instance while it is being provisioned. 
	(pic:IAM-role-created-and-assigned-before-ec2.png)

	# Command line testing with role created before hand. 
	[bash]
	ssh ec2-user@EC2IP KeyPair.pem
		yes
	sudo su
	clear
	aws ls s3
		all buckets
	cd ~ #home directory
	ls -a #show hidden files
		. .. .bash_history .ssh ...
	[end]

	!Roles are more secure than storing the access key and secret access key
	Roles are easy manage and also update policies. 
	Roles can only be assigned while EC2 is being provisioned.
	!Roles are universal, you can use them in any region. 
-----------------------------------------------------------
    Bash Scripting
-----------------------------------------------------------
	todo:
	Install apache
	run updates
	move files from s3 to folder and run web server
	steps:
	create a s3 bucket and upload an html file in to it. 
	create ec2 machine in the same region

	[bash]
	#!/bin/bash (pronounced as sha-bang)
	#interpreter takes all these commands and runs them in root level
	yum install httpd -y
	yum update -y
	aws s3 cp s3://(get name of bucket)/index.html /var/www/html/
	service httpd start
	chkconfig httpd on
	[end]
-----------------------------------------------------------
    EC2 Instance Metadata
-----------------------------------------------------------
	Access the metadata from command line
	tasks:
	Create new EC2 instance. 
	!Instance meta data url: http://169.254.169.254/latest/meta-data

	[bash]
	ssh ec2-user@EC2IP KeyPair.pem
		yes
	sudo su
	yum update -y
	clear
	#get metadata from url
	curl http://169.254.169.254/latest/meta-data
		ami-id
		ami_launch_index...
	#get public ip v4 address
	curl http://169.254.169.254/latest/meta-data/public-ipv4
		IP address
	[end]
-----------------------------------------------------------
    Auto Scaling Groups and Launch Configurations
-----------------------------------------------------------
	tasks:
	create a healthcheck.html > I am healthy
	create launch configurations
	before creating auto scaling group, you need to create launch configuration.
	[bash]
	#!/bin/bash (sha-bang) initialize the bootstrap scripts
	yum install httpd -y #installing apache
	yum update -y #updating the os
	aws s3 cp s3://BUCKETNAME/ /var/www/html/ --recursive #copy all contents
	service httpd start #starting the apache
	chkconfig httpd on #keep the apache service running if server restarts
	[end]



























































