URLS are permitted to only contain the printable characters in the US-ASCII char set.
  Ex: ASCII code 0x20 to 0x7e (inclusive)
        Exceptions: A few reserved characters, because they have a special meaning in the HTTP protocol (or within the URL scheme itself.)

URL-encoding scheme
  Is used to encode any problematic characters within the extended ASCII character set, so that they can be safely transported over HTTP.

  URL-encoding formatting
    % (2 digit ASCII code expressed in hex)

    Exs:
      %3d
        =
      %25
        %
      %20
        Space
      %0a
        New line
      %00
        Null byte

      Note: + character also represents a URL-encoded space


  When pentesting, you should URL encode ANY of the following characters when you insert them as data into an HTTP request.
    space % ? & = ; + #
      Obviously, you will need to use these characters' special meaning when modifying a request (ex: adding a query string).  In this case, you should use the literal form.

    

