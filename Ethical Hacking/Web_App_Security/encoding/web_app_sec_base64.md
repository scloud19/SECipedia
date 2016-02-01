Base64 Encoding
  Allows any binary data to be safely represented using only printable ASCII characters.

  Common Usages
    Encode email attachments for save transmission over SMTP.

    Encode user credentials in basic HTTP auth.

  How it works
    Base64 processes input data in blocks of 3 bytes.
      Remember: 8 bits = 1 byte.  A bit is 0 or 1.
      
      Each of these blocks is divided into four chunks of six bits each.
