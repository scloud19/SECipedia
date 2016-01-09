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

    