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

