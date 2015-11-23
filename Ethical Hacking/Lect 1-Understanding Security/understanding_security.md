Currently at 4:44 attack vectors

Cool resources
	cybermap.kaspersky.com
	map.ipviking.com

Why is hacking an issue?
	High reward/low risk for hackers

Hacking definition
	Making something do something that it wasn't initially desgined to do

Common definitions
	Zero-day Attack
		Basically, an attack that will succeed.  There's no patches for this attack, etc.
	Daisy-chaining
		Pawning a machine and using that as a vector into something else.  (Or a similar type of attack).  
			Can use it to pawn someone in marketing, and then use that as a vector into another server.  An inexperienced sysadmin will just look at the logs and then blame the wrong person
	Hackivism
		Hacking for a cause, not for money, etc.
	pwned
		Origin: Someone fat-fingered "owned"
		Basically, I own you

Data Breach Stats by Industry
	Healthcare leads the way
		SSN, income, etc.

Creating your lab
	Have a few VMs that can talk to eachother but can't talk directly to the host machine
	If you need windows ISO's, look at the first ethical hacking course on pluralsight.  There's a way to get Windows images for a 180 eval (with a command to extend it an additional 2 times)

Potential vectors for attack
	Hosts
		Footprinting threat
			Each system responds a certain way to stimulus, thus you can deduce what a system is
		Physical Security
		Bad password policies
			When it comes to passwords, it's all about length
		Denial of Service
			People cant get access to a given service
		Privilege Escalation
			Break into a current user's account, and escalate their privileges.  The blame will go on them
		Backdoors
			Ease of access later after break-in
			Service accounts
				Email servers and other services often have accounts.  If you use one of these names, you can mask an account from a sysadmin
		Physical
			Giving away old hardware, etc. without completely scrubbing the hard drive.
				When you delete files, they aren't usually "deleted"
				EX: Copy machines storing print images in HD.  After these machines have their leases finished, China is the biggest buyer of them.

	Applications
		Configuration
			People usually use the defaults and dont think
		Buffer Overflow
		Data input validation
			Ex: mySQL injection

	Human
		What do your employees expose about your infrastructure on a social network site?
		Monitoring job application posts to look at infrastructure of company.

	Networking
 		Sniffing/Eavesdropping
 		ARP (Address Resolution Protocol) Poisoning
 			ARP = Resolves an IP address to a MAC address
 			You can reroute traffic through your machine before it goes out to the internet
 		Spoofing
 		Routers and "non-human facing systems"
 			When was the last time you updated the firmware on your router?

