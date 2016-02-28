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

  Process
    1) Alice and Bob agree on the encryption also and a secret key
    2) When Alice wants to send some data to Bob, she uses the secret key to encrypt the data.
      Bob will leverage the same key to decrypt it.

  Safe as long as the key is a secret


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
          Opens up a number of attacks

      Solutions
        block cipher modes
          Attempt to address the limitations
          Sometimes add authentication into the process        

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
