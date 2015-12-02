MiM = Man in the middle

Vulnerabilities
	You have to compile mongo from source utilizing an --ssl flag to have encryption on both the intracluster level and between mongo and clients.
		Must people probably don't do this.
		Ways that this connection can be MiM?

Potential Vulnerability
	How many people make their clusters authenticate with eachother via a --keyFile option?	
		Fake Mongo servers can try to access the cluster without this option and get a high level of access

	Ways to spoof IP to get localhost exception?
		This will allow an attacker to get user creation rights, etc.


