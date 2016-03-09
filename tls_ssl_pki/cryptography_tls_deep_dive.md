TLS 1.2
  Record Protocol
    TLS is implemented via the record protocol
    In charge of transporting (and optionally encrypting) all messages exchanged over a connection
    Length limit - 16,384 bytes



    Key features
      Message transport
        Transports data submitted to it by other protocol layers
        

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

