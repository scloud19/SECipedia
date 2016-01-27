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

    Note that sometimes the decoding doesn't always occur on the server.  
      Ex: The html encoded iframe
        <iframe src=j&#x61;vasc&#x72ipt&#x3a;alert&#x28;1&#x29; >
          Equals
            <iframe src=javascript:alert(1)>

            Some browsers perform a HTML decode on the src parameter value.  So, these types of attacks can be on a persistent XSS variety, and their encoding will evade XSS filters (with the decode occurring on the client).


    Converting from one character set to another
      These types of vulnerabilities occur because some technologies perform more of a "best fit" mapping of characters based on graphical similarities.
        Exs: « can be converted into <
            Ÿ and Â == Y, A

          These types of attacks can be leveraged to bypass initial filters (if they occur before a given conversion.)


