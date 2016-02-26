software_signature
  When given a file, we need to make sure that the file isn't adulterated or has been currupted in transit
    To do this, a piece of software can be given a 'detached signature' by its developers to vet is as legitimate
      Ex: Doing th GnuPG (GNU Privacy Guard)
        1) Download the signature
          Usually has an *.asc extension

          $ gpg --verify signature.asc
            gpg: Signature made Wed 12 Aug 2009 23:27:06 BST using DSA key ID E77B534D
            gpg: Can't check signature: public key not found

          As you don't have the referenced key yet, simply import it from the server

          $ gpg --recv-keys E77B534D
          $ gpg --verify signature.asc
            If you get "Good Signature" you should be okay.
              The other warnings, you can generally ignore.
