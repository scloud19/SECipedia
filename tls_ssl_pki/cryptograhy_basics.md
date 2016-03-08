Cryptography
  keeps secrets (confidentiality), verifies identities (authenticity), ensures safe transport (integrity)

Common Terms
  plaintext
    data in the original form
  cipher
    the algo used for encryption
  ciphertext
    the data after encryption
  exhaustive key search
    Try all possible decryption keys to get the plaintext
      keyspace/computationally secure
        if the key is selected from a large keyspace and breaking the encryption requires iterating though a prohibitively large number of keys, then the cipher is computationally secure.
  x-bit key
    Ex: 40 bit key
      Is 5 bytes
      40 bit length corresponds to a total of 2^40 possible keys
  entropy
    The randomness collected by an application (or OS) for use in cryptography
      Often collected from
        Hardware sources
        Mouse movements
        Keystrokes
        Specially provided randomness generators 
          Entropy collected from these sources is a type of true random number generator (TRNG)

          true random number generator (TRNG)
            CONS
              The approach is not reliable enough to use directly
                Ex: You might need to generate a 4096-bit key, but the system might only have a few hundred bits of entropy available
                  If there aren't enough external events to collect enough entropy, the system can stall
                    Thus, in practice, pseudorandom number generators (PRNGs) are often leveraged
          pseudorandom number generators (PRNGs)
            seeding
              Use small amounts of truly random data to initialize
            From the seed, PRNG produce unlimited amounts of pseudorandom data

            CONS
              While PRNGs can be leveraged in programming, they aren't cryptographically secure (even if the output looks statistically random)
                Thus, we need cryptographically secure PRNGs (CSPRNGs)

                CSPRNGs
                  PRNGs that are also unpredictable

Cryptographic primitives
  the building blocks of cryptography

  Alone aren't useful, but very powerful when combined

Random Number Generation
  Cryptography is based on the quality of random number generation
    Problems
      Computers aren't very good at random number generation
        This is true because true random numbers can only be obtained by observing a certain physical processes
          In the absence of this, computers focus on collecting small amounts of entropy

Symmetric Encryption
  aka private-key cryptography

  Pros
    Great at handling large amounts of data at fast speeds
  Cons
    Problems when multiple parties become involved
      Ex:
        Members of the same group must share the same key
          The risk for compromise increases

  Process
    1) Alice and Bob agree on the encryption also and a secret key
    2) When Alice wants to send some data to Bob, she uses the secret key to encrypt the data.
      Bob will leverage the same key to decrypt it.

  Safe as long as the key is a secret

Asymmetric Encryption (public key cryptography)
  Most popular asymmetric encryption method
    RSA
  Keys
    Uses two keys instead of one (as seen in symmetric encryption)
    2 Keys: Public and Private
      Both of these keys are linked to eachother
        Public -> Private
          If you encrypt data with someones public key
            Only their corresponding private key to decrypt it
        Private -> Public
          If Data is encrypted with someone's private key
            Anyone with the public key can decrypt
            Use Case
              Digital signature
                Now you can vet the authenticity of a message
  Pros
  Cons
    Rather slow and unsuitable for use with large quantities of data
      limitations vs symmetric encryption
        Computationally expensive
          Asymmetric encryption must do a computationally more difficult task: it must be able to publish the encryption key without revealing the decryption key
        Network bandwidth
            In symmetric encryption, an attacker can't initially encrypt (because they hopefully don't have the private key).  Hence, under ideal circumstances, determinism isn't an issue and we don't need the randomness
            
            In asymmetric encryption, to mitigate determinism, it must add in an element of randomness.
              This adds to the file size
                Ex: RSA with a 1024-bit key
                  You can encrypt a data element only up to 117 bytes, which yields a 128-byte value


      For these reasons asymmetric encryption is leveraged for authentication and the negotiation of shared secrets.  In turn, these are utilized for faster symmetric encryption.

Cryptography strength
  often measured using the number of operations that need to be performed to break a particular primitive
    This is presented as bits of security

  Accuracy
    The strength of cryptography can't be measured accurately
      Due to this, you'll find many different, albeit similar, recommendations
    ENISA 
      European Union Agency for Network and Information Security

      Provides guidance on what types of crypto to use for certain use cases
        To view ENISA and other recommendations
          https://www.keylength.com/

    Additional Info
      https://www.feistyduck.com/library/bulletproof/online/ch-ssl-tls-crypto.html
        Table 1.3
          An encryption strength mapping for common key sizes

    Security is a function of
      time
        Changes of strength into the future
          "items are secure today, and insecure in the future"
        
        The strength of encryption changes
          Why?
            Computers will continue to get faster/cheaper

      resources
        A key size could be difficult for an individual to break, but easy for an organization

      Thus, when looking at the strength that you need, always ask
        Secure is whom? and for how long?

    
      

    Bits of security
      Ex: 
        128 bits of security (2^128 operations)
          is sufficient for most deployments
        256 bits of security
          Need very long-term security or a huge safety margin
  
      Symmetric cryptographic operations
        Adding one more bit of security makes the key twice as strong

      Complications
        Not all operations are equiv in terms of security
          Ex: For a given security need, there are different bit values for Symmetric, Asymmetric, DH, Elliptic Curve, and Hash functions
          Ex: Table 1.2 https://www.feistyduck.com/library/bulletproof/online/ch-ssl-tls-crypto.html#protocols

  Encryption strength
    often measured via the key length

    It's assumed that the keys are random
      This means that the keyspace is defined by the number of bits in a key
        Ex: 128-bit key
          Considered VERY secure
          One of 340 billion billion billion billion combinations


Ciphers
  Are divided into two groups
    Stream and block ciphers
  
  Block Ciphers
    Encrypt entire blocks of data at a time
      Blocks - Usually defined as 128 bits (16 bytes)
    For every possible input combination, there is exactly one output
      Given that the key stays the same
    A small variation in input produces a large variation in output
      Ex: a change of one bit anywhere
    Can be utilized as the basis for certain cryptographic primitives
      Ex: hash functions, message authentication codes, pseudorandom generators, stream ciphers
    Most popular
      AES (Advanced Encryption Standard)


    Problems
      You can only use them to encrypt data lengths equal to the size of the encryption block
        In real life, data doesn't always have this fixed length.
        
        EX: 128-bit AES (worlds most popular block cipher) requires 16 bytes of input data
          This produces 16 bytes of output
            What if you have LESS data than this?

          Solution:
            Padding
              The extra data that is appended to the plaintext to produce the required block size

              The padding must follow a format that allows the receiver to know that it IS padding
                It can't be random
                Ex TLS Padding:
                  The last byte of the encryption block contains the padding length
                    This byte communicates how many bytes of paddings (excluding the padding length byte) there are.
                    The padding bytes are set to the same value as the padding length
                      Ex:https://www.feistyduck.com/library/bulletproof/online/figs/padding-example.png


      Deterministic
        Always produce the same output for the same input
          aka without an element of randomness
          
          Opens up a number of attacks

            Asymmetric encryption problems
              (or anytime an attacker knows the input and output)
                Even if the output is encrypted, these problems still hold

              In asymmetric schemes, anyone can leverage a public key and encrypt a message.  Thus, the attacker can know the input and output
                Without an element of randomness, an attacker can simply enumerate until they get an input that matches the encrypted output that their attacking.

      Solutions
        block cipher modes
          Cryptographic schemes that extend block ciphers
          Allow encryption of data of variable length

          Support confidentiality
            But, only some block ciphers combine it with authentication

          Certain modes transform block ciphers to stream ciphers

          Block cipher modes
            ECB (Electronic Codebook)
              Only supports data lengths that are multiples of the block size
                If you have data of a different length, then you need to apply padding before
              Encryption process
                1) Split the data into chunks that match the block size
                2) Encrypt each block individually
              Simplest mode possible


              As block ciphers are deterministic, so is ECB
                Problems that arise
                  Patterns in ciphertext will appear that match patterns in plaintext

                  An attacker can detect when a message is repeated

                  An attacker can submit variations of plaintext for encryption, and observe the output
                    Commonly possible in HTTP
                    From brute forcing, can deduce patterns and guess other plaintext

                    Ex: BEAST attack against TLS

              An example of how not to design a block cipher mode
            CBC (Cipher Block Chaining Mode)
              The main mode in SSL/TLS
              The next "step up" from ECB

              Produces a Initialization vector (IV)
                To mitigate determinism
                Random
                  Produces different output for all inputs
                    *Even if the inputs are the same
              Process
                Ex: https://www.feistyduck.com/library/bulletproof/online/figs/crypto-cbc.png

                1) Generate a IV that is the same length as the encryption block
                2) The first block of plaintext is combined with the IV using XOR
                  Ensures that the ciphertext is always different
                3) Preform encryption
                4) Utilize the ciphertext as the IV for the next stage in the chain (hence the name)
                  Perform this step until all data has been encrypted
                5) Transmit the ciphertext and IV to the receiving party

            
            GCM
              Currently the best mode available
              Relatively new addition to TLS
              Provides confidentiality and integrity

  Stream Ciphers
    Most popular
      RC4
        Became popular due to its speed and simplicity
        No longer considered secure
    keystream
      The key is fed into the cipher, which produces an infinite stream of seemingly random data
    Encryption process
      XOR logical operation
        One byte of the keystream is combined with one byte of plaintext using the XOR operation.
        Ciphertext
          The result of the XOR operation
          Ex:
            Key -> Stream Cipher -> 1 (Keystream) | 1 (Plaintext) = 0 (Ciphertext)
    Security Test
      The encryption process is considered secure if the attacker can't predict which keystream bytes are at which positions.
        For this reason, with stream ciphers, it's important that a key is only utilized once.
          Why?
            Ex: 1)Encrypting HTTP requests
              Attackers know plaintext at certain locations (protocol version, header names, etc.)
              2) As one knows the ciphertext, they can combine this with the plaintext to deduce certain parts of the keystream.
              3) If the same key is used in future ciphertexts, you can utilize this information to uncover the same parts of future ciphertexts
          Mitigation
            Stream algorithms are utilized with one-time keys derived from long-term keys

      Encryption
        You feed 1 byte of plaintext to the cipher, and the output is 1 byte of  ciphertext

        This occurs for every byte in the stream that's fed into the cipher
          

      Decryption
        This process is simply reversed 

  Digital Signatures
    A cryptographic scheme that allows one to verify the authenticity of an entity

    Strength 
      Depends on
        The individual strengths of the encryption, hashing and encoding

    Are possible through asymmetric encryption
      A message signed by a private key can be vetted with a public key
      The exact approach depends on the cryptosystem
      Ex Creating a signature with RSA
        Note
          Not all digital signatures are created as seen with RSA
          RSA is different because it can be leveraged for both encryption AND digital signing
            DSA/ECDSA
              Popular public key algos
              Can't be used for encryption and thus rely on different approaches for signing
        1) Calculate a hash of the document that's being signed
          The size of the document doesn't matter
            The output will always be fixed
              Ex: output = 256 bits for SHA256
        2) Encode the resulting hash and some additional metadata
          The receiver needs to know the hashing algo to utilize before she can process the signature
        3) Encrypt the encoded hash using the private key
          The result of this step is the signature
          You can add this to the document to VET its authenticity

      Ex: Vetting a signature with RSA
        1) Receiver takes the document and calculates the hash using the same algo
        2) Uses your public key to decrypt the message and recover the hash
          In this process
            Vets that the correct algo was used
            Compare the decrypted hash with the one that was calculated in step 1

    MAC
      See cryptography_hash_functions.txt
      Is a type of digital signature
      CONS
        Still require the sharing of a private key

Protocols/Schemes
  An ideal protocol will provide these items (if the situation needs it)
    Three phases
      Handshake 
        Includes authentication and a key exchange scheme
        Authentication
          Is the other party really who they say they are?
          Ex: Can leverage public key cryptography to authenticate each party. For example, one party can create a random number and ask the other to sign it.

          Key exchange scheme
            Negotiates encryption keys securely
              Ex RSA Key Exchange:
                Party 1 could generate all the keys and send them to Party 2 by encrypting them with Party 2's public key
              Ex: Diffie-Hellman (DH) key exchange protocol
                Slower, but has better security

      Data exchange phase
        Provides confidentiality and integrity
      Shutdown sequence
        A special message to mark the end of a conversation
          Without it, a MiM attack could prematurely end the conversation

  Combine cryptographic primitives into something useful
  Aim to provide
    confidentiality, integrity, authentication

  Sequence numbers
    Are assigned to each message that's sent
    Are a part of the MAC calculation

    For MiM attacks, this mitigates the attackers ability to
      drop messages
      replay a sent message

    Warning Signs
      If there's a gab in the numbers
        There's a message missing
      If there's a duplicate number
        We know that there's a replay attack












