Unicode Encoding
  Is a character encoding standard that is designed to support all of the world's writing systems.

  For pentesting, Unicode encoding is of interest because it can sometimes be leveraged to defeat input validation.
    Ex: An input filter blocks certain malicious expressions, but later, the function that processes this input understands Unicode, you can potentially do a bypass with various standard and malformed Unicode encodings.

  It employees various encoding schemes, some of which can be used to represent unusual characters in web applications.

  16-bit Unicode encoding
    Works in a similar fashion as URL encoding
    
    When transmitting over HTTP, the 16-bit Unicode-encoded form of a character is the %u prefix followed by the character's Unicode code point (in hex)
      Ex:
        %u2215
          /

        %u00e9
          é


  UTF-8
    Is a variable-length encoding standard that employs one or more bytes to express each character.

    For transmission over HTTP, the UTF-8 encoded form of a multibyte character simply uses each byte expressed in hex and is delimited by %

      Ex:
        %c2%a9
          ©
