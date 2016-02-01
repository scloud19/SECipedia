Base64 Encoding
  Allows any binary data to be safely represented using only printable ASCII characters.

  Always look out for Base64 that is issued to the client.
    You can easily deduce if an string is encoded in Base64 by looking at its simple character set (see below) and the potential = padding on the end.

  Common Usages
    Encode email attachments for save transmission over SMTP.

    Encode user credentials in basic HTTP auth.

    Many web apps use Base64 to transmit binary data within cookies and other params, and even to "hide" sensitive data to prevent modification.

  How it works
    Base64 processes input data in blocks of 3 bytes.
      Remember: 8 bits = 1 byte.  A bit is 0 or 1.

      Each of these blocks is divided into four chunks of six bits each.
        Six bits of data allows for 64 different possible permutations, so each chunk can be represented using a set of 64 characters.


      Base64 character set
        Contains only printable ASCII characters

        Ex: ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/

    If the final block of input data results in fewer than three chunks of output data, the output is padded with one or two = characters
      Ex: hello
        Base64: aGVsbG8=



