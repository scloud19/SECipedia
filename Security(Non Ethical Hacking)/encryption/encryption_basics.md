Symmetric-key Encryption
  encryption_pics/symmetric

  Utilizes the same key to encrypt and decrypt the data.

  The security of the key is where the security of the information rests.

  vs Asymmetric-key Encryption
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

    In general these hashes can be used to quickly detect duplicate data (hash tables), for fingerprinting, and as checksums to detect accidental data corruption (ex: in networking and data transmission) (double check hash tables and checksums).

  Digital Signatures
    See digital_signatures.png
    
    Combines the general ideas of asymmetric-key encryption and Hashes

    These signatures give us confidence that the file that we've received is really from who we think it's from, and then it hasn't been changed since it was "signed"

    Ex:
      Software dev creates a file and we download it.
        But, is this the same file that the developer created?  This problem is solved through a digital signature for the file.

        How does this signing process happen?
        The developer creates a hash of the file, then encrypts that hash with a private key.  The output of this encryption process will generate a digital signature.

        When the user receives the file, they also receive this signature.  This signature is then decrypted with the site's public key, which reveals the hash that the developer created from the file.

        At this point, the user creates their own hash of the file (using the same hashing algo) and verifies that the 2 hashes match.
          If these 2 hashes do match, then the signature is valid.

        Key Assumptions:
          The public key that we've utilized is actually the true public key of the site.










    