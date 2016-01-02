Questions: What is the difference between session and transport.  They seem to have some overlap.

OSI Model:
    Application:
        Where our end users interact with the network.
        Makes sure our remote communication partner is available and makes sure that there's an agreement (between the two) on:
            Data integrity
            Privacy
            Error recovery
    Presentation
        "How should this data be presented?"
            This isn't the layer that shows the data on the screen (that's the application layer.)
        You can think of this layer as a formatting layer.
            Ex: If you opened up a PDF in a non-PDF app, all you see is jumbled text.
                This is a presentation layer issue
        Encryption
    Session
        Manager of the overall data transfer process.
        Is in charge of the creation, maintenance, and teardown of the communication channel the two parties are using.
    Transport
        Establish a logical end-to-end connection between two parties (however it does much more).
        Usually involves UDP (User Datagram Protocol) or TCP (Transmission Control Protocol)
    Network
        Deals with 2 primary questions:
            "What paths exist from point A to point B?"
            "Of those paths, what is the best way?"
        The IP addresses that we are familiar with (ex: 172.1.3.1) are used at this layer
            In other words, routing is done at this layer.
    Data Link
        The primary device that we'll interact with at this layer is a switch.
        Switches run at this layer as well as
            Ethernet
            HDLC (High Data Link Control)
            PPP (Point-to-Point Protocol)
            Frame Relay
        The MAC address runs at this layer and it's AKA:
            Layer 2 address (Data Link is the second layer)
            Hardware address
            Burned-in address (BIA)
                Address is burned into your NIC (Network Interface Card).
                    However, can't we change this?
            Physical address
                Remember this DOESN'T mean that the MAC address exists on the physical layer

        General Terms: Error detection vs. Error correction
        Remember, Error detection is different than Error Correction.  Just because you’re detecting something doesn’t mean that you’re correcting it.

        The data link layer performs error detection via the Frame Check Sequence (FCS)
            Steps for the FCS:
                1)Sender runs a mathematical formula against the contents of the frame

                2) The sender places the result of that value into the FCS field of the frame, and then sends it

                3) The receiver of the frame runs the same algorithm against the contents of the frames.  If the resulting value matches that in the FCS field, the frame is fine.  If that resulting value doesn't match, that frame is considered corrupt and is then discarded.

                However, why don't we have error recovery?
                    It's impossible to reverse on the receiving end.
    Physical Layer
        Is about sending data with binary (1,0's which symbolizes electrical currents being turned off and on)

        Includes anything to do with physical cable or the standards in use OR the electrical current, etc.

        Overall Data transmission process (but easier to think of is the "data chopping process")
            Once the end user sends some data, that data goes through all seven layers of the OSI model.
                At the transport layer, the data starts to be segmented into smaller units, and each layer below the Transport Layer will break the units up into even smaller units.
                    At the end, the data will be transformed into a stream of 1,0s that can successfully be transmitted by the Physical Layer

        So if the physical layer needs binary, how does that happen?
            The transport layer begins taking the data and segmenting it into smaller units.  Every layer below this layer will continue this process until the binary at the physical layer is created.

    As a network admin, most of your work is in the physical, data link and network layers.

    Terminology of the "data" at different layers
        At the Application, Presentation, and Session layers, data is simply referred to as "data."
            The "breakdown" of data hasn't started
        At the Transport Layer, this data segmentation begins and the data is placed into "segments"
        More data classifications:
            Network Layer: "Packets"
            Data Link Layer: "Frames"
            Physical Layer: "Bits" aka binary
        Each layer creates a layer specific header that will be removed by the layer on the other end of the session.
            The only exception is the Data Layer that adds a header and a trailer.  This trailer is removed on the other side of the session by the corresponding Data Layer
        An example of this is the data starting off at the Application level of the User... "bubbling down" to the Physical Layer.. and then "Bubbling up" to the server on the other end.
        The combination of data and a layer-specific header is a Protocol Data Unit (PDU) and there's a PDU for all layers
            Ex: L6 PDU (for Layer 6)
        Same layer interaction vs adjacent layer interaction
            Same Layer interaction:
                Ex: An OSI layer on one end of the session removing the header placed on it by the same layer at the other end of the session
            Adjacent-layer interaction: The interaction between layers of the OSI model on the same host.
                Ex: The application layer can have interactions with the presentation layer.  The presentation layer can have adjavent-layer interaction with both ther Application and Session layers (the ones directly above and below it), and so both.

TCP / IP Networking Model
    Application
        Maps to OSI layers:
            Application
            Presentation
            Session
            (Besides that, direct mapping)
    Transport
    Network
    Data Link
    Physical

When troubling shooting EVERYTHING, always start at the physical layer and move your way up the OSI model

TCP vs. UDP
    Similarities
        Operate at the Transport layer of the OSI model
        Perform multiplexing

    TCP
        Guarantees the delivery of segments
            Three Way Handshake (TWH)
                REF tcp_handshake pic
                RH - Requesting Host
                RecH - Receiving Host
                1) Requesting host sends a TCP segment with the SYN bit set (SYN = SYNchronization) to RecH.
                    SYN Packet: seq = 0, and ack = 0
                        Initializes its own seq number (ISN)
                            0 is a relative number to the starting numbers for simplicity.
                        The seq and ack are headers in the packet
                2) RecH host responds with a TCP segment with the SYN and ACK bit set (ACK = ACKnowledgement).
                    This tells RH that the request was recieved
                    "ACKnowledgemening the SYN bit"
                    SYN/ACK Packet: seq = 0, ack = 1
                        Creates it's initial sequence number
                        Takes previous seq number and adds 1
                3) RH sends an TCP ACK packet to RecH
                    "I recieved your SYN/ACK"
                    ACK Packet: seq = 1, ack = 1
                        Takes previous hosts seq number and adds 1 to ACK
                        Increments its own SEQ number
                After TWH is complete, RH sends its request with the last seq and ack numbers.
                    Ex: "Get /": seq = 1, ack = 1. len = 120 bytes <-- Which is derived by looking at the size of the packet itself (not including the HEADERs)
                    Sets the sequence number from the ACK response
                RecH then sends a response
                    Ex: 301 redirect: seq = 1, ack = 121.  Len = 70 bytes
                        It takes the length of the segment it recieved and adds 1 to it.
                RH can make another request
                    Ex: GET /login: seq = 121, ack = 71, len = 130 bytes



                    The TWH is now completed
                When the stream is no longer needed, a TWH is initiated to tear down the communication 
            Performs error detection
                If packets are recieved out of order, they can be reassembled 
            Performs windowing
            Connection Oriented
                Two-way communication between the two endpoints will take place befire data is exchanged 
            How TCP Detects and Recovers from Lost Segments
                Is deduced from the ACK f
                This is not technically error detection


    UDP
        Non-guarenteed delivery
        No sequence or ack numbers
            Thus no error detection
        No windowing
        "Connectionless"
            There's no communication prior to data exchange
        Used in DHCP and voice and video protocols



