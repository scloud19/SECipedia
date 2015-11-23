Amazon CLI
	Allows us to get rid of the console (aka GUI)
	Can leverage bash scripting to automate
	aws - invokes CLI

	aws SERVICE_NAME help

	Configure credentials through linux
		These were initially set up in IAM through Users (and potentially by adding a user to a group)
			aws configure
				enter access key ID and Secret access key
				default output format
					Can specify JSON

				These settings are stored in ~/.aws/config
					Thus you could manually add everything


				Once the creds have been saved to a given EC2 instance, you can now login to a service (s3, etc.)  This is assuming that the ec2 service has a role of s3 access

				In general, you don't want to store your access key, etc. on the EC2 instance.  This is for security reasons.  The example above is to illistrate a process, but don't do it in PROD.
					You should always use roles in IAM instead of storing your keys locally on an EC2 instance.
						Look in IAM file for an example of this

	s3 bucket access
		aws s3 ls s3://BUCKET_NAME
		aws s3 LOCAL_FILE s3://BUCKET_NAME