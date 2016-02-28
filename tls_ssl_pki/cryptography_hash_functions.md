Hash function definition
  An algorithm that converts input of variable length into a small, fixed-size output
Result of a hash function
  A hash

Commonly used
  In programming

Hash functions vs. cryptographic hash functions
  Cryptographic hash functions have additional traits
    Preimage resistance
      Given a hash, it's unfeasible to find the plaintext that created it.
    Second preimage resistance
      For a given plaintext (and its hash)
        It's unfeasible to find a different plaintext with the same hash
    Collision resistance
      It's unfeasible to find plaintexts that have the same hash