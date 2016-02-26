Network Layer
  At its core, the Internet is built on top of the IP (Internet Protocol) and TCP (Transmission Control Protocol)
    These are both leveraged to package data into small packets for transport.
      hops
        These packets travel across many computer systems (aka hops) in many different countries
          Without encryption, anyone with access to these communication links can gain full access to the data as well as change the traffic without detection

To prevent impersonation attacks, SSL/TLS rely on PKI (public key infrastructure) which makes sure that the traffic is sent to the correct recipient.

Goals of TLS
  Cryptographic security
    Enable secure communication between any two parties who wish to exchange information

    Cryptography
        also see cryptography_basics.md
      keeps secrets (confidentiality), verifies identities (authenticity), ensures safe transport (integrity)
  Interoperability
    Independent developers should be able to develop programs/libraries that are able to communicate using a common cryptographic API
  Extensible
    TLS is a framework for the development/deployment of cryptographic protocols.
      It should be independent of the primitives of cryptography (Ex: ciphers, hashing functions, etc.)
        This allows for migration to one cipher to the next, etc.
    Efficiency
      Minimize the performance cost
        Ex: Provides a session caching scheme to keep cryptographic operations down to a minimum.
