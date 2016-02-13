Base64
  Block based format in which every 4 bytes of encoded data, translates into 3 bytes of decoded data.
    If your attempts to decode are weird, simply start from four adjacent offsets into the encoded string.


  A common mistake is to begin decoding at the wrong position within the string.
    With the way that Base64 works, if this occurs, you will get a wrong answer
