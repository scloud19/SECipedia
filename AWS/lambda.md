Lambda
	A compute service that runs your code in response to events and automatically manages the underlying compute resources for you.	 

	 What events trigger lambda
	 	Table updates in DynamoDB, modifications to objects in S3 buckets, messages arriving in an Kinesis stream, AWS API call logs created with CloudTrail, and custom events from mobile applications, web applications, or other web services

	 Supported Programming Languages
	 	Javascript
	 	Double check if more

	 Availability
	 	99.99% for both the service itself and for the functions it operates

	 Pricing
	 	Requests
		 	First 1 million requests are free
		 	$.20 per 1 million requests there after
		 Duration
		 	Calculated from the time your code begins executing until it returns or otherwise terminates, rounded up to the nearest 100ms.  The price depends on the amount of memory you allocate to your function.  YOu are charged .0000167 for every GB-second used

	Free Tier
		1M free requests per month and 400,000 GB-seconds of compute time per month.  The memory size for you choose for your Lambda functions determines how long they can run in the free tier.  THe Lambda free tier does not automatically expire at the end of your 12 Month Free Tier term, but is available to both existing and new AWS customers indefinitely.

	RAM for Lambda functions
		128 MB to 1024 MB

	Pricing Example
		Allocate 512 MB of memory to your function, executed in 3 million times in one month, and it ran for 1 second each time, your charges would be calvulated as follows
			Monthly computer price is .00001667 per GB-s and the free tier provides 400,000 GB-s

			Solution
				Total compute time = 3M seconds
				Total compute (GB-s) = 3,000,000 * 512MB/1024 = 1,500,000 GB-s 

				Total compute - Free tier compute =  Monthly billable compute GB-s
				1500000 GB-s - 400,000 free tier GB-s = 1,100,000 GB-s

				Monthly compute charges = 1,100,000 * .00001667 = $18.34