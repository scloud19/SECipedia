command - C
note - n
TO DO:
	Lecture 11 of CCNA udemy
	Look at hub_bridge_switch.png


S: Sender
R: Reciever

General Terms/Information:
	Unicast traffic
		Destined for one host
	Multicast traffic
		Destined for a group of hosts
	Broadcast traffic
		Destined for everyone
	Network interface controller (NIC)
		Connects a computer to the network
	Switch	
		Used to connect computers, printers and servers together and serves as a controller of communications amongst the devices.
			Umanaged Switches
				Works right out of the box
				Has less network capacity than managed switches.
				Usually found in home networking equipment
			Managed Switches
				More Configuarable, offers greater flexibility and capacity.
				You can monitor and adjust the switch locally or remotely.
	Full Duplex vs. Half Duplex
		Full duplex allows for data communication to be sent and recieved at the same time.  Half duplex only is uni-direcitonal.

	Router
		Connects networks together.
			Ex: Router connects computers to the internet so users of the network can share the connection

			Acts as a dispatcher, choosing the best path of information to travel.
	Switch vs Router
		Switch creates a network while a router connects networks.

	Any port number below 1024 is a reserved, well-known port number
	Good ports to know:
		TCP 20: FTP (file transfer)
		TCP 21: FTP (control)
		TCP 22: Secure Shell (SSH)
		TCP 23: Telnet
		TCP 25: Simple Mail Transfer Protocol (SMTP)
		TCP/UDP 53: Domain Name System (DNS)
		UDP 67,68: Dynamic Host Config. Protocol (DHCP)
		UDP 69: Trivial FTP i.e. TFTP
		TCP 80: Hypertext Transfer Proto
		TCP 110: Post Office Proto. (Current version: 3)
		UDP 161: Simple Network Management Proto.
		TCP 443: Secure Sockets Layer ("HTTPS")
		UDP 514: Syslog
		UDP 546, 547: DCHP (for IPv6)

	Socket: An IP address with a port
		127.0.0.0.1:80
		Sometimes you will see it in this format
			IP, transport protocol, port number


Three-way handshake will occur during the construction of a TCP session.  The four-way handshake will occur during the DESTRUCTION of the handshake.



During three-way handshake, an ACK is sent after every packet is recieved.  If no ACK is recieved for a packet, the packet is then resent

The sequence number and the ACK number are what is used to detect and recover from lost segments.

The sequence numbers tell the reciepent what order the segments should be reassembled

Acknowledgement during transmission (After thee-way handshake)
	See trans.png

	Say server1 is sending 4 segments to server2.
	Each segment has a sequence number and it's that number that tells server2 what order the segments should be reassembled
	After a prenegotiated number of segments have been recieved, server2 will acknowledge that all of the segments have arrived safely.
		This occurs by sending a segment with no data in it.
		The ACK number will be set, but it will be set to the next segment number that server2 EXPECTS to see.
			Ex: 7500 in the picture
		This process is known as the cumulative acknowledgement scheme and it allows TCP to realize when segments have been lost.
		If one is missing, server2 will send an ACK set to the SEQ number that was missing.
			S1 will retransmit this segment
			After S2 receives the retransmited segment, it will send an ACK with the next SEQ number that it expects to see.
				Ex: trans2.png

			Q: How does S1 know how many segments it can send before it has to receive an ACK?

			Q: Why doesn't the recipient just send an ACK for every segment it recieves? 
	During the three-way handshake, the initial size of the window is negotitated.  This determines how many bytes of data the recipient is willing to take in before it has sent an ACK.

		The size of the window is dynamic and is expressed in bytes, thus is called the sliding window.
		The recipient can up the size of the window if it starts handling the initial size easily (this is called flow control).
			Similarily, the recipient can lower the size as well.

Similarities in UDP and TCP
	Multiplexing
		Ex: We have 1 IP sending info to a server that includes information via:
			HTTP
			SMTP
			FTP
		Q: How do we keep track of all of this different information coming in at the same time?
			A: Ports
				Allows S & R to send mixed data at one time.
					This is called multiplexing
					Thus we have 1 datastream that includes streams from the protocols listed above
	Headers in both UDP and TCP
		Source port
		Destination port
		Checksum

UDP
	A lot smaller than TCP (not so many headers, but less features)

TCP 4-way handshake.
	Ex: 4-way.png
	Each device needs to send a segment with the FIN bit set and they need to ACK the FIN they recieve
	(Won't be on CCNA exam.)

Ethernet
	Frame Consists of:
		Header
		Data
		Trailer

		More specifically it's the
			Preamble
				There for synchronization pruposes.  Don't need to know more for the exam
			SFD (Start Frame Delimiter)
				Preamble has ended and the destination MAC address is on deck
			Destination
				MAC address
			Source
				MAC address
			Type
				The protocol type carried in the data field.  Today, that's usually IPv4/IPv6
				but it can be others
			FCS
				The ethernet trailer
				An error detection tool
					First, the sender runs an algorithm against the contents of the frame, and takes the result of that algorithm and puts it in the FCS field.  The result is the checksum.
					Second, the reciever gets the frame and runs the same algorithm and compairs that with the checksum.
					Third, if the values of the checksums don't match, the frame is dumped.  There's no explicit notification from the receiver to sender that the frame was discarded.  
				Error detection, but not error correction
	Most cables are Unsheilded Twisted-Pair (UTP)
	Q: Why twist the cables?
		Cuts down on electromagnetic interference (EMI)
	EMI
		Can come from other cables (or elevators)
			Try to isolate cables as much as you can from eachother
		"Cross Talk" can occur from other wires in the same cable giving EMI
			Near-end crosstalk (NEXT)
				Can occur when wires are crossed or compressed.
					If the conductors are too close (even if not directly exposed), there can be EMI

	8 MegaBits (mb) = 1 megabyte (MB)
	10Base-T (802.3) <-- IEEE Spec Num (know this)
		10 MegaBits Per Second (Mbps)

	100Base-T (802.3u) aka Fast Ethernet
		100 MegaBits Per Second (Mbps)

	1000Base-T (802.3ab) aka Gigabit Ethernet
		10000 MegaBits Per Second (Mbps)

	10GBase-T (802.3an) aka 10 Gig Ethernet
		10 GB Per Second (GBps)

	There is a version of Gig Ethernet that runs on fiber-optic cable.
		802.3z aka 1000Base-LX
		Max cable length of 5000 meter
			All other ethernet cables have a 100 meter max
		Other ethernet runs on copper cable which is cheaper

	If we are moving from different standards (i.e. speeds) of ethernet during the transfer of a frame, we don't have to do any translation process
		This is because of ethernet standards have the same overall frame format (some very minor differenences)
			Header
			Data
			Trailer

		Autonegotiation
			CURRENTLY AT 7:33
			
			Tool that allows us to use network and host devices with different Ethernet capabilities
				Basically the autonegotiation is the beginning conversation where the nodes tell eachother what their capacity is, etc (speed ability, duplex ability, etc.)
					This occurs via the Fast Link Pulse (FLP) which is in contrast to the Normal Link Pulse (NLP).

					NLP is sent by an ethernet device when there's nothing else to send.  It basically is a heatbeat that says, "I'm still here"

					In autonegotation, full duplex is always perfered to half-duplex

					Autonegotation will automatically adjust if the port capacity changes (i.e. the PC now has a faster ethernet card)

					Always utilize this feature on both ends or dont use it at all
						You might see this situation on the exam, but not in the real world

						If only one end is using autonegotiation, usually the end with it enabled can detect the correct speed.  However, if that speed is equal to or less than 100 Mbps, the autonegotiation will set the overal link to a half-duplex connection.  Even if the other host is capable of full-duplex

						When there is a duplex mismatch, the devices can still communicate, but it will be a slow, inefficient process


			Has made large improvements over the years
			Mandatory for Gig ethernet over copper


Cabling
	For ethernet cables, they end with an RJ-45 connector
	For fast ethernet and ethernet, there's 4 wires inside the cable. (Pins 1,2,3,6)

	Gigabit Ethernet uses 8 wires



	Straight-Through Cables
		The wires on the inside of the cable are stright
			Pin 1 in one side, maps to Pin 1 on the other.
		Ex: A PC's network card sends on pins 1 and 2 and a switch sends on pins 3 and 6.  In turn, the PC recieves on pins 3 and 6, and the switch recieves on pins 1 and 2.  Thus we can use a straight-through cable

	Crossover Cables
		Necessary when we're trying to connect devices of the same type.
			Ex: Switches
				Trunks
					Switch to Switch connections
				If we left this device with a straight-through cable, the same pins would be sending data and pins 3,6 would be listening for data that would never be recieved
		On Ethernet and Fast Ethernet
			Wire on Pin 1 crosses over to Pin 3
			Wire on Pin 2 crosses over to Pin 6
			Wire on Pin 3 crosses over to Pin 1
			Wire on Pin 6 crosses over to Pin 2
		
		On Gigabit Ethernet
			In addition to the above pins on Ethernet/Fast Ethernet
				Wire on Pin 4 crosses over to Pin 7
				Wire on Pin 5 crosses over to Pin 8 	
				Wire on Pin 7 crosses over to Pin 4 	
				Wire on Pin 8 crosses over to Pin 5 	

		Memorize the pins for the exam

Five names, one address
	MAC address (Media Access Control)
	Physical address
		The address physically exists on the network card
	Layer 2 address
	Burned-In address (BIA)
		Address is physically burned into the NIC
	Ethernet address

	The MAC address is used by switches to send frames to the proper destination in the most efficient manor possible.
		Six bytes long (48 bits)
		Formats
			Are comprised of HEX values
			aa-bb-cc-11-22-33
			aabb.cc11.2233
		MAC address has two parts
			Organizationally Unique Identifier (OUI)
				Assigned to hardware vendors by IEEE
				It is unique to the vendor
			Second half
				A value not already used by the hardware vendor within the particular OUI.
			EX:
				aa-bb-cc <--OUI part

		Special MAC address used for broadcast frames
			Address
				ff-ff-ff-ff-ff-ff (can be uppercase or mixed case too)
					Case doesn't matter in HEX

					Currently at 4:38

Connecting a computer to a router or switch (for configuration/troubleshooting)
	You'll be connecting to the Console port on the network device
	Need a console cable
		8 wires inside the rollover cable and they each roll over to a different pin at the other end.
			Pin 1 to Pin 8
			Pin 2 to Pin 7
			Pin 3 to Pin 6

			One end will have a RJ-45 connector, the other will have a DB-9 connector.
				You will probably have to get an adapter from the DB-9 so your laptop can use it

Repeaters and Hubs
	Layer 1 devices
	Repeater
		To mitigate against attenuation
			Def: a signal loosing its power as the length between two nodes increases
		Takes an incoming signal, and then generates a new, clean copy of that exact signal
	Hub
		Operates in the same fashion as a repeater, but has more ports
		On the exam, these types of devices can be denoted with a box and an arrow like <---->

		Can connect multiple computers together, but only 1 computer can send data at one time
			If a collision occurs (which is likely) all data involved in the collision is unusable.
			Hubs make a single collison domain and a single broadcast domain
			Single broadcast domain isn't good because every PC has to inspect broadcast packets


		To mitigate the problems with a hub, hosts on a shared Ethernet segment will use Carrier Sense Multiple Access with Collision Detection (CSMA/CD)

		You won't see repeaters and hubs in todays PROD networks

CSMA/CD
	Process
		1) A host that wants to send data will first listen to the wire to see if it's in use

		2) If the media is in use, the host backs off for a few ms before listening to the wire again.

		3) If the media is not in use, the host sends the signal.

		4) If two PCs send data at the same time, the voltage on the wire will change, indicating to the hosts that there has been a data collision
			The PCS that send the data will generate a jam signal, which indicates to the other hosts on the wire hat they shouldn't send data right now.
				These PCs will then invoke a unique backoff timer, which is a random timer in the ms range.  When each timer expires, they will try the CSMA/CD process again (which includes listening to the wire), and sense the timer is random, it's unlikely the two hosts will have this problem again.


Bridges and Switches
	Help with data collisions and unnessary broadcasts

Bridge
	Created smaller collision domains (less collisions), is used to segment networks.
		The "bridge" bridges two networks
		Helps with collisions, but not unnessary broadcasts (everyone on the network still gets those)

Switch
	Can replace hubs, repeaters, and bridges
	The universal representation of a switch is a box with two <---> on it

	When you connect each computer to a switch, that creates a unique collision domain for that host
		Thus collisions literally can't occur and multiple hosts can transmit simultaneously

		More bandwidth for each host (and more granular bandwidth)

	Cisco switches by default, dont breakup broadcast domains

	Microsegmentation
		"One host, one collision domain" segmentation performed by switches

	Frame Forwarding Decision-Making Process (for a Cistco switch)
		1 of 3 things will occur when an incoming frame arrives
			Forward it
				The switch does have an entry for the frame's destination MAC address.  It's sent out only to the port that leads to that destination MAC.
			Filter it
				Filtering = not sending a frame out a port
				Source and destination MAC addresses are known to the switch and are both found off the same port.  This means the frame is dropped.

				Technically, if all MAC addresses are known, and Host A sends a frame to Host C.  You could say that the frame is filtered on port 3 (i.e. 0/3) because it is dropped on that port because it isn't going to Host D.
			Flood it
				The switch has no entry for the frame's destination MAC address.  A copy of the frame is sent out of every port on the switch except the one it dcame in on. Unknown unicast frames are always flooded.

	Broadcast frame : In addition to flooded frames, is type of frame is sent out every port except its arrival port.

	Q: When a frame enters a switch, what does the switch look at first?
		A: The source MAC address first, destination MAC address second
			Why?
				The source addresses are how the switch builds and maintains its dynamic MAC address table

				In general: There are dynamic routing protocols that operate at Layer 3 (RIP, EIGRP, OSPF) and these can be used to build a routing table.
					Switches don't have an equivalent to these so a switch builds its MAC address table by looking at the incoming frame's source MAC address.

	We could build a MAC address table with static entries, but that has serious drawbacks
		Too inefficient of an approach, that's why we using dynamically learned MAC addressed.  

			For dynamically learned MAC addressed
				Basically, as long as the switch hears from a host within any given 5 min period, the host's MAC address stays in the table.
					c- Mac address-table aging-time 600
						Switches the aging time to 10 minutes (600 seconds)

				If a host gets reconnected to the switch on a different port.
					Once the host sends frames into the switch, the switch realizes the "switch" and removes the old MAC to port mapping and makes a new entry in the MAC address table.

	Ex: p1.png
		Host A sending a frame to Host C
			Frame comes in on Port 0/1, and the switch looks at the source MAC address and the switch looks for the MAC address its table.
				If there isn't an entry, one is made.
				So if we do our show mac-address-table
					Vlan = 1
					Mac Address = aaaa.aaaa.aaaa
					Type = DYNAMIC
					Ports = Fa0/1
			At this point the switch has the frame forwarding decision (Forward/Filter(drop)/Flood) AND
			The switch examines the destination MAC address, "bb-bb-bb-bb-bb-bb" ans sees if the destination MAC address is in the MAC table
				If there isn't a destination MAC address (which is true in this case), the switch "floods the frame"
					Unknown unicast frame: This is the frame in this situation because the frame is unicast (destined for one particular host) but the port that directly leads to that distination is unknown.
					Flooding the frame (aka broadcast): A copy of that frame is sent out of every single port on the switch except the port the frame came in on.
			Once the frame gets to Host C, it responds with a frame of it's own with a destination of Host A.  		The frame has the source and destination MAC addresses
				As this is routed through the switch, the switch looks at the source MAC address of that frame.  Since there isn't an entry for cc-cc-cc-cc-cc-cc the switch creates one
					We can see this at C: show mac address-table dynamic
			Now, the switch checks the MAC table for the destination address aa-aa-aa-aa-aa-aa
				It finds it at routes it to the correct port (0/1)
					This is a known uni-cast frame, so nothing is flooded

		Topology is the same as the previous example, but lets assume now that there's a dynamic MAC entry for each host.
			The hub in the diagram doesn't happen in the real world.  Also notice how the hub/switch is graphically shown in the diagram.

			To the switch, hosts A/B are found off of port 0/1.

			If Host A --> Host B, Host B recieves a copy (as well as the switch)
				The switch then looks at the source and destination MAC address and realizes that both of them are off of the same port.
					At this point, the switch "filters the frame" aka it simply drops the frame.


	In general, switches never send a frame back out the same port that the frame came in on.



At 9:09 at lec 13

C - show mac-address-table
	When router is initially powered on, these entries are initially for the CPU's.

	this table is AKA switching table, Content Addressable Memory (CAM) table, and the bridging table (even though it isn't a bridge).