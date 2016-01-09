Symmetric Cryptography
  encryption_pics/symmetric

  Utilizes the same key to encrypt and decrypt the data.

  The security of the key is where the security of the information rests.

  vs Asymmetric Cryptography
    Usually, a pair of keys is made utilizing an algorithm
      Keys generated
        Public key
          Made public, think of this as a public "padlock".
            
        Private key
          Think of this as the combination that unlocks the "padlock"

          Made private and held in a file.
            This file is typically itself encrypted with a passphrase.

      Ideas behind public/private keys
        A person can't deduce one key from another.

        Information that is encrypted with one key, can be decrypted with another.
          Typically, we encrypt with the public key, and decrypt with the private key.  Thus only people with the private key can unlock the data.

          Additionally, you can encrypt with the private key and decrypt with the public key.

Hashes (all of this assumes that the hash is done CORRECTLY)
  A hash is a one-way transformation
    An individual can't reverse engineer the hash back to the original input (aka "message")
    
    Variable length input, fixed-length output
      Output can be called
        message digest, digest

    The ideal hash
      It is easy to compute the hash value for any given message

      One can't reverse engineer a message from a hash

      Once can't find two different messages with the same hash
        If you can (and this has rendered certain hashing Algorithms useless (MD5?)) then there can be some serious security implications (see digital signatures section.)

    Ex:
      "Hello World" ---> Apply hashing function ---> 743j8k2k9jjskdfj836 (the hashed output)

      In general, the original message can be much shorter (or much larger) than the hash.  

    What's the point?
      If two hashes are the same, we can have a high degree of confidence that the inputs are the same.






    