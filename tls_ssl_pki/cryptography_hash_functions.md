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
  A hash function can be utilized for this
    ONLY if the hash of the data is transported separately (from the data itself)
      Security reasons
        Attacker could modify the message and the hash
  Message Authentication Code (MAC)
    Aka keyed-hash
    
    Problems with standalone encryption:
      Produces confidentiality, but not integrity
      An eavesdropper cant decrypt the ciphertext, but they could replace the legitimate ciphertext with their own.
        Solution
          When a MAC is sent with the ciphertext, the legitimate communication partners (who all share the hashing key) can be sure the message hasn't been tampered with.


    Cryptographic function that extends hashing with authentication
    
    Only those who have the hashing key can produce the MAC

    Commonly used in combination with encryption



