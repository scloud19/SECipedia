Hash function definition
  An algorithm that converts input of variable length into a small, fixed-size output

  AKA
    fingerprints, message digests, digests

Popular
  SHA1
    Has an output of 160 bits
    Considered weak
      Should utilize SHA256

Strength of a hash
  Is at most one half of the hash length
    Deduced from the "birthday paradox"
      A well-known problem in probability theory
    Contrast to ciphers
      Where the strength is equal to its length


Result of a hash function
  A "hash"

Commonly used
  In programming
    Ex: A compact way to represent large amounts of data
      These hashes can then be compared very quickly

Hash functions vs. cryptographic hash functions
  Cryptographic hash functions have additional traits
    Preimage resistance
      Given a hash, it's unfeasible to find the plaintext that created it.
    Second preimage resistance
      For a given plaintext (and its hash)
        It's unfeasible to find a different plaintext with the same hash
    Collision resistance
      It's unfeasible to find plaintexts that have the same hash

Verifying Data integrity
  Problems with standalone encryption:
    Produces confidentiality, but not integrity
      Ex: An eavesdropper cant decrypt the ciphertext, but they could replace the legitimate ciphertext with their own.
        Solution
          Sending a MAC with the ciphertext         
  Message Authentication Code (MAC)
    EX: https://upload.wikimedia.org/wikipedia/commons/thumb/0/08/MAC.svg/661px-MAC.svg.png
    
    Short piece of information used to authenticate a message
  
    MAC algorithm
      Cryptographic function that extends hashing with authentication
      SOMETIMES called a keyed (cryptographic) hash function
        there can be other creation mechanisms
      Input 
        A secret key (hashing key) and a variable length message
      Output
        A MAC
          aka tag
          This MAC is sent with the ciphertext to the receiver

      Vetting
        The receiver of the message runs the ciphertext (and hashing key) through the MAC algorithm
          If the MAC matches what was sent, the message is valid




