General Notes
	Architecting within the VPC
		Typically you have different subnets for different parts of your application.
			Ex: DB tier in one subnet that doesn't have internet access, etc.  You can then control the communication between these subnets.
				For example the app server, which has a public IP (if it's in the DMZ) can be set up to communicate with a non-internet facing tier through a private IP address
	Security Groups
		Can be utilized across different subnets (or AZs).
			Remember a subnet can't cross multiple AZs
	Elastic Ip address
		A static IP address
			You can mask the failure of an instance or software by rapidly remapping the address to another instance in your account.
		This address is associated with your AWS account, not a particular instance

		All AWS accounts are limited to 5 EIPs because of IPv4 limits.

		It is strongly recommended to use an elastic IP address primarily for the ability to remap the address to another instance in the case of instance failure, and to use DNS hostnames for all other inter-node communication
VPC
	Virtual data center in the cloud that is logically isolated from outside
	AWS ecosystem.
	You can launch AWS resources in a cirtual network that you define.

	Complete control. Including control of
		virtual networking env
			Can launch instances (EC2, RDS, etc.) into a subnet of your choosing
		
		creation of subnets
			selection of your own IP address ranges for each subnet
				If you have an existing IP range you can bring them over (i.e. extend them)
			public vs private subnets
				public subnets are internet accessible

		configuration of route tables between subnets

		network gateways
			You can create internet gateways that allow for a subnet to be accessed via the internet
				By default, when you create an internet gateway it's detached.  You must attach it to ONE VPC.
					You can only have one internet gateway per VPC

		Much better security control over your AWS resources
			security groups (firewall for inbound/outbound traffic for a resource)
			subnet network access control lists (ACLS)

	Ex: You can create a public-facing subnet for your webservers that has access
	to the internet.  You can place backend systems such as databases or app servers
	in a private-facing subnet with no internet access.  You can leverage multiple layers of security, including security groups and network access control lists to help control access to help control access to EC2 instances in each subnet.

	Ex: Create a Hardware VPN connection between your corporate datacenter and your VPC and leverage the AWS cloud as an extension of your corporate datacenter

	Custom VPC vs Default VPC
		Default VPC
			Already created when you create your account
				You get a default VPC in each AWS region

			User friendly, allowing you to immediately deploy instances
			All subnets in default VPC have an internet gateway attached

			Each EC2 instance has both a public and private IP
				You can use a private IP addresses to communicate between instances in the same VPC.


			If you delete the default VPC the only way to get it back is to contact AWS
	VPC Peering
		Allows you to connect multiple VPCs together (in one AWS account or between many)

		You connect one VPC to another via a direct network route using private IP addresses

		Instances will behave as if they were on the same private network

		Archecture
			Star configuration
				Ex: 1 central VPC with 4 others on "arms"
				This way, all traffic between VPC's has to go through the central node

	Restrictions
		5 VPCs per region (can be increased by request) <-- Only restriction on exam
		5 internet gateways per region
		5 elastic IP addresses per region per AWS account (can be increased by request)
		50 VPN connections per region
		50 Customer Gateways per region
		200 Route tables per region
		100 Security Groups per VPC 
			50 rules per security group 

		5 internet gateways 

	Creating a VPC
		Your VPCs
		Create VPC
			CIDR block
				Formatting for your IP subnet (aka your IP range)
				Most common is: 10.0.0.0/16
					AWS recommends that you utilize a CIDR block from the private
					(non-publicly routable) IP address ranges.
						EX: 10.0.0.0/16 or 192.168.0.0/16
							It is possible to specify a range of publicly routable IP addresses, but AWS Doesn't support direct access to the internet from publicly routable CIDR blocks in a VPC.
								Thus it doesn't make sense to do a public IP range
			Tenancy
				Dedicated
					Puts your VPC on a dedicated server. Thus, any resources that you launch in the VPC will be on that dedicated server.
						This setting overrides settings for tenancy elsewhere

		After the VPC has been created
			A route table will automatically be created for the new VPC
				A route table specifies how packets are forwarded between the subnets within your VPC, the internet, and your VPN connection.

				Every route table entry has an associated subnet
					A subnet is always mapped to 1 AZ (not many)
			In the routing table, if you see a target of local, that any communication within the the "destination" CDR block or in this case, the VPC's CIDR block (which contains the subnets you create in the overarching VPC block) can communicate with eachother.
				Thus, an EC2 instance launched in one subnet of VPC 1 can communicate with another AWS services in another subnet of VPC 1.
					To communicate, just look at the EC2 instance's private IP address
Routing Tables
	If you don't associate a subnet with a route table, they will utilize the main table routes by default.
		A subnet can't utilize multiple routing tables
		The main route table is the one that was automatically created with the VPC
	When creating an internet gateway, you need to create a routing table for that gateway (which exists inside a given VPC)
		Select the Routes within this route tables
			Edit the table to allow for  internet access
				Select "target" and place in the internet gateway

				Destination - 0.0.0.0/0 means that we're allowing all traffic
		Subnet Assocations tab
			Once you associate the internet routing table with a given subnet, it will then have internet access.

Launching an EC2 instance into a VPC
	Go through the steps associated with launching an EC2
		Step 3 - Configure Instance
			Network
				Select your VPC
			Subnet
				Select the subnet within your VPC
					If you want your EC2 to have access to the internet,
					launch it into a subnet of your VPC that has an internet gateway
					attached in the routing table (for that subnet)
Connecting security groups
	Scenario:
		What if we have an EC2 instance connecting to an RDS mySQL db?
			We need to connect the security group of the EC2 instance (think of outbound) to the security group of the rds service (think of inbound)
				We accomplish this in a VPC
					This step is needed so the two items can communicate with eachother

					Open VPC
						Security Groups
							RDS Security Group
								Inbound Rules
									Type - MySQL
										Click in Source field
											Select the security group that you'd like to link to

											This says "Allow MySQL traffic from this security group"

	Ex: We have 2 subnets (1 public, 1 private). In the private subnet, we have a DB (the CIDR block is 10.0.2.0/24), how do we give it access to the internet for updates (i.e. our public subnet)? In this scenario, we only want to allow HTTP/HTTPS traffic.
		We need to create a NAT (Network Address Translation) instance
			This instance sits between the public and private subnets.

			Allows private subnet instances the ability to speak to the internet but you can disallow ssh, etc

		To get this going
			Create a security group in EC2 and assign it to the VPC of the subnet
				Inbound/Outbound Rules
					Type 
						HTTP/HTTPS
					Destination
						Custom IP (for inbound)
							10.0.2.0/24
						Anywhere (for outbound)
							You can obviously make this more granular
			Create a NAT instance
				Launch EC2 instance
					Step 1 -Choose AMI
						Community AMI's
							Search for NAT
								amzn-ami-vpc-nat-hvm-*
					Step 2 - Choose Instance Type
						Can select t2.mircro for the instance
							If there is a bottleneck, you need to increase this.  You will get more network throughput
					Step 3 - Configure Instance
						Network - choose your VPC
						Put it into your public subnet

						Auto-assign Public IP
							Disable - We add the public IP manually
								We don't have to do this, but will in this case to show an alternative method.
									Remember - Putting an instance in a public subnet is not enough to give it access to the internet. You either have to assign a public IP address (or elastic ip address) or place the instance behind an elastic load balancer.
					Step 6 - Configure Security Group
						Add in the security group that we created for the NAT
			Adding the Elastic IP (optional)
				We're utilizing this because we declined the auto-assign Public IP address

				In EC2
					Elastic IPs
						Allocation New Address

						Associate Address
							Click inside "Instance"
								Select the NAT

			Change the Source/Dest. Check of the NAT
				Ec2 console
					Instances
						Select the NAT
							Actions
								Networking
									Change Source/Dest. Check
										By default, an EC2 instance must be the source/destination of any traffic it sends/recieves (respectively).  You must disable this manually for a NAT
			Need to allow traffic from NAT to the internet
				VPC console
					Select the main route table for the VPC in question
						Add a route
							Destination - 0.0.0.0/0
							Target - NAT instance
	ACLs
		Act as firewalls that allow you to put down network rules across entire subnets
			This firewall is placed in front of any security group, so if you had port 80 open on an instance via a security group, but the ACL denied this port, that instance wouldnt recieve traffic

		A subnet can't be associated with multiple ACLs
			An ACL can be associated to multiple subnets

		Are stateless

		A numbered list of rules that are sequentially evaulated with the lowest number first.
			These allow/deny traffic in or out of any subnet associated with the ACL
		Start with rules that are multiples of 100 so you can insert new rules later on

		A VPC is given an ACL by default
			Allows all inbound/outbound traffic

		You can create custom ACLs
			These default to deny all inbound/outbound traffic

		If you don't associate an ACL with a subnet, it is given the "default ACL"

	Summary of a typical VPC Creation process
		Create a VPC
			Define your CIDR block (IP Address Range)

			By default this created a Network ACL & Route Table

		Create a Custom Route table
		Create your subnets
		Create Internet Gateway
			Attach to our VPC
			Attached a custom route for a given subnet that we want to make public
		Provisioned an EC2 instance for NAT in our public subnet
			Created a security group
				Allowed inbound connections to CIDR blocks in private subnets
				Allowed outbound connections on HTTP and HTTPS for all traffic
			Disabled Source/Destination Check
			Set up a route on our private subnets to route through the NAT instance for all traffic