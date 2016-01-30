Replay attack
  Ex replay attack:  A eavesdropper (Eve) intercepts an encrypted message that initiates a transaction.  If the Eve wants to, she can leverage a "replay" attack and try to "replay" what she intercepted.  Without having to decipher the message, she can simply replay the encrypted message to initiate an unintended transaction.
            If a nonce value (only used once) is added to the encrypted message, the server can see that the nonce value has already been used and disregard the message.

        