Canonicalization
  The process of converting/decoding data into a common character set.

Vulnerabilities
  Double Encoding
    Encoding user request parameters twice in hex in order to bypass security controls or cause unexpected behavior from the application.
      This bypasses the initial security filters that only decode user input once.  If another process on the backend decodes any parameters again (and there's no whitelisting), the effects can be severe.
        Ex: A path traversal attack ../
          ../ is represented by %2E%2E%2f
          When % is encoded again, its representation in hex is %25.  Thus,
          ../ is equal to %252E%252E%252F
          You can do triple encoding, etc. 

    Ex: An application strips an apostrophe instead of blocking it, and then performs further canonicalization
      ' in hex: %27

      Potential bypass
        %%2727
