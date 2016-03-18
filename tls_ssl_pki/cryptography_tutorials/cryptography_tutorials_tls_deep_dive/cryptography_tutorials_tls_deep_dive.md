TLS 1.2
  Record Protocol
    TLS is implemented via the record protocol
    Core competencies
      In charge of transporting (and optionally encrypting) all messages exchanged over a connection
    Length limit - 16,384 bytes

    Subprotocols (see separate files)
      // We will be going over these soon
      handshake protocol
      change cipher spec protocol
      application data protocol
      alert protocol

    Key features of the record protocol
      Message transport
        Transports data (i.e., buffers) submitted by other protocol layers
        If a buffer is longer than the record length limit
          the record protocol fragments it into smaller chunks
        the record protocol can also assemble smaller chunks into a singular record

      Encryption and integrity validation
        On a brand new connection
          Messages are transported with any protection
            This has to occur so that the first negotiation can take place.
            Once the handshake is complete
              The record layer starts to apply encryption and integrity validation according to the negotiated parameters
      Compression
        Not leveraged
          Many people already compress their outbound HTTP traffic
          CRIME attack
            Was the "final blow" and rendered this type of compression obsolete due to insecurity
      Extensibility
        Everything outside of core competencies (see above), the record protocol will delegate to subprotocols

        Pros
          Makes TLS extensible
            With encryption handled by the record protocol, all subprotocols are automatically protected using the negotiated connection parameters
            


    TLS record composition
      Header
        Contains info about
          record content type (or subprotocol)
          protocol version
          length
      Data

      Sequence Number
        Each TLS record is assigned a unique 64-bit sequence number
        This is kept internally and not sent
        Purpose
          To keep track of the number of records sent by the other side
          Is part of the defense against replay attacks

  Handshake Protocol
  subprotocol of the record protocol
  The most elaborate part of the TLS protocol
  
  Performs the SSL handshake
    the sides negotiate connection parameters and perform authentication

  requires 6-10 messages (depending on features leveraged)

  Highly variable
    Contingent on
      configurations 
      supported protocol extension

  Message format
    Header
      Message type
        1 byte
      Length
        3 bytes
    The rest the message depends on the message type

  3 common use cases
    full handshake with server auth
    abbreviated handshake that resume an earlier session
    handshake with client and server auth

  Full handshake
    Every TLS connection begins with a handshake

    When is this used?
      If the client hasn't previously established a session with the server, the two sides execute a full handshake
        This is utilized to negotiate a TLS session

    Full handshake process
      The client and server will perform 4 main activities

      1) Exchange capabilities and agree on desired connection parameters
      2) Perform key exchange
        a) Validate the presented certificate(s) or authenticate using other means
        b) Agree on a master secret that will be used to protect the session
      3) Verify that the handshake messages haven't been tampered with.

      CURRENTLY AT
        NOTE
          In practice, steps 2 and 3

