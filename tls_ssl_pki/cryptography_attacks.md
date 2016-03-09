Ways to attack
  "Cryptography isn't bypassed, not attacked"
    The primitives are solid, but the software ecosystem isn't
    
  Brute Force
    Ex: Trying to brute force a key
    These attacks usually require a lot of time/CPU power
  
  Analytical attack
    Leveraging known vulnerabilities
    Often the best way

  Man-in-the-Middle Attack (MITM)
    Constitute most attacks against TLS

    Passive network attack
      An attacker is just listening in on the conversation

      unencrypted traffic

      encrypted traffic
        Even if it can't be broken now, one can store the information until technology catches up

        forward secrecy
          Problem
            Computer systems often contain a critical configuration weakness that allows for decryption of all previously recorded internet traffic

            The most common key-exchange mechanism in TLS is based on the RSA algo
              The RSA key used for the key exchange can also be used to decrypt all previous conversations
                Thus RSA doesn't support FORWARD secrecy.
                  CounterPoint: 
                    You can achieve forward secrecy with RSA if you generate short lived key-pairs. But most protocols that provide forward secrecy use Diffie-Hellman, probably because generating DH keys is cheap and easy compared to RSA keys. – CodesInChaos♦ Aug 1 '14 at 8:13

    Active network attack
      Attacker is actively modifying the traffic/influencing the conversation

      Difficulties
        Active attacks are more difficult than passive passive because
          Passive attacks only need to make copies of observed packets (this is easy)
            Active attacks require a lot more software/hardware/technique
              Rerouting traffic,keeping track of individual connections, serving false certs
                Rerouting large amounts of traffic is difficult to do undetected
                False certs are difficult to use successfully for large-scale attacks because many organizations keep track of what certs are used by various websites.

      Best chance of subversion relative to difficulties
        


      In its usual incarnation, MITM attacks target authentication
        Alice thinks that she's talking to Bob
          Instead of doing a key negotiation with Bob, Alice is fooled into doing a negotiation with Mallory
          Although, Alice's messages are being intercepted by Mallory (and Mallory forwards the messages to Bob)
        It doesn't matter if Alice's messages are encrypted
          Mallory has the keys needed to decrypt

      TLS MITM
        The ideal case for Mallory
          She can present a cert that Alice will accept as valid
          If this occurs, the attack is seamless
        Unideal case for Mallory
          Present an invalid cert to ALice and hope that Alice accepts the cert warning

    Attack sequence
      In many cases, attacks require proximity to the
        victim
        server
        communication infrastructure
          As a packet goes across the internet, it stops at many at many "hops" aka routers
            If one has access to the cables or routers, they can potentially launch a MITM.
              This can either be in collaboration with the telecoms, or by hacking the equipment
      
      Easiest way
        Join a network and rerouting a victims' traffic through a malicious node
          This is relatively easy on an open access WAP

      Other ways to start an MITM
        ARP spoofing
          ARP (Address Resolution Protocol)
            Used on local networks to associate network MAC addresses with IP addresses

          Can only be used on networks that use the ARP and is limited to LAN segments

          Is a technique by which the attacker sends a spoofed ARP message onto the LAN.
            The aim is to associate the attackers MAC address with the IP address of another host (such as the default gateway).
              The default gateway in computer networking is the node that is assumed to know how to forward packets on to other networks

              If the attacker emulates the default gateway, she can intercept/modify all traffic and then route it to the real default gateway (which then interfaces with the internet)
                Or, the attacker can simply take the network down

          This attack allows
            stop all traffic, modify traffic

        WPAD (Web Proxy Auto-Discovery Protocol) hijacking
          Used by browsers to automatically retrieve HTTP proxy configs
          Attacking WPAD
            Starts a proxy on a LAN and announces it to the clients who look for it.

        DNS hijacking
          Changing the
            IP associated with the registrar
            DNS config
          An attacker can reroute all traffic intended for a domain name

        DNS cache poisoning
          Enables the attacker to inject invalid DNS information into the cache
          After the attack, all users of an affected DNS server will be routed to a malicious IP

        BGP route hijacking
          Border Gateway Protocol (BGP)
            A routing protocol leveraged by the core internet routers to discover where exactly IP address blocks are located
          If an invalid route is accepted by one or more routers, all traffic for a particular IP address block can be redirected to an attacker.

  Protocols
    There's attacking the protocol itself and then the implementation of the protocol

    Attacking Implementation
      aka attacking the software libraries that implement the cryptography

      C/Assembly
        Most crypto libraries are written in these languages
          Thus, due to the complexity of the languages, it's easier to exploit bugs

  Timing attacks
    An attacker can break encryption by observing how long certain operations take

Attack Surface (relative)
  low
    Cryptographic primitives
      Well understood and do only one thing
  medium
    schemes
      Add additional complexity
  high
    protocols
      add a lot of complexity

