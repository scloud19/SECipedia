Many cryptographic hacks can be mitigated by following relatively simple rules
  Use well-established protocols and never design your own schemes
  Use high-level libraries and never write code that deals with crypto directly

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

  Use well-established primitives with strong bits of security
    Complications
        Not all operations are equiv in terms of security bits
          Ex: For a given security need, there are different bit values for Symmetric, Asymmetric, DH, Elliptic Curve, and Hash functions
      
      For looking at protection levels and what I need, find a table like this
        Ex: Table 1.2 https://www.feistyduck.com/library/bulletproof/online/ch-ssl-tls-crypto.html#protocols

        This table was in 2012, so find a recent one
          Recommended: ENISA
          If wanting others
            keylength.com

      Also note
    Additional Info
      https://www.feistyduck.com/library/bulletproof/online/ch-ssl-tls-crypto.html
        Table 1.3
          An encryption strength mapping for common key sizes


  Use a key exchange mechanism that supports forward secrecy
      aka DONT use RSA
        Counterpoint to RSA assertion:
          You can achieve forward secrecy with RSA if you generate short lived key-pairs. But most protocols that provide forward secrecy use Diffie-Hellman, probably because generating DH keys is cheap and easy compared to RSA keys. – CodesInChaos♦ Aug 1 '14 at 8:13
      