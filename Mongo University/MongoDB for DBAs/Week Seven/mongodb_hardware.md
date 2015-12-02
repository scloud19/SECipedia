Helpful Links
	https://docs.mongodb.org/manual/administration/production-notes/?_ga=1.189605099.1940964481.1446699147

Hardware
	For true production level servers, the true costs could be around 8k-10k PER server
		This is from the Mongo University DBA course

	Think of a 2 axis graph (cost vs power)
		You want to stop where the marginal gain of power is prohibitive vs cost.

	If having to make a direct comparison between CPU clock vs more CPU cores
		More CPU clock wins.


	Want 64-bit

	Disable NUMA, if your server uses it for memory management.

	SSDs are key
		Leave 20% of the drive unpartioned
			Minimizes the write amplification

	Mongo looks like it is utilizing a tremedous amount of RAM, but it yeilds this to other system processes when needed

	Check the 	

	Virtualization
		Mongo works fine in these ENVS (this includes EC2, etc.)
		
		Problem platforms
			OpenVC sometimes can have issues
