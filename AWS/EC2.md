Key terms
	AZ - Availability Zone
	instance type
		determines the hardware of the computer used for your instance

	AL - Amazon Linux

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
		Amazon Linux AMI
			Security
				AL AMIs are configured to automatically install security updates at launch time.

			Package management
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
