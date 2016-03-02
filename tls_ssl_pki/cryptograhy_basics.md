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


Cryptographic primitives
  the building blocks of cryptography

  Alone aren't useful, but very powerful when combined

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
        Computation
          Asymmetric encryption must do a computationally more difficult task: it must be able to publish the encryption key without revealing the decryption key
        Network bandwidth
          To mitigate determinism, as


      For this reason asymmetric encryption is leveraged for authentication and the negotiation of shared secrets.  In turn, these are utilized for faster symmetric encryption.
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
