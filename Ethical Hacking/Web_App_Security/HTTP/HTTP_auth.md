Challenge/Response
  A family of HTTP protocols.  "Challenge" from the server (or place that is implementing authentication) and "Response" is what the user provides.
    Ex: Most basic is password based authentication

The HTTP protocol includes its own implementation for authentication.
  This includes
    
    Basic
      Simple auth mechanism that sends user credentials a Base64-encoded string in a request header with each message

    NTLM
      Challenge-response mecahnism and uses a version of the Windows NTLM protocol


    Digest
      Challenge/response authentication.

      Is an application of MD5 cryptographic hashing with usage of nonce values to prevent replay attacks.

        Ex replay attack:  A eavesdropper (Eve) intercepts an encrypted message that initiates a transaction.  If the Eve wants to, she can leverage a "replay" attack and try to "replay" what she intercepted.  Without having to decipher the message, she can simply replay the encrypted message to initiate an unintended transaction.
          If a nonce value (only used once) is added to the encrypted message, the server can see that the nonce value has already been used and disregard the message.