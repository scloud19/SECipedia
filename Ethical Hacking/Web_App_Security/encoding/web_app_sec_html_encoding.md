HTML Encoding
  Used to represent html's "special characters" so that they can safely incorporated into an HTML document.

  When pentesting, your main interest in HTML encoding is likely to be when probing for XSS vulnerabilities
    If the app doesn't HTML-encode its input, it's probably vulnerable.

    Ex: Certain characters have special meaning within HTML and are used to define a document's structure (rather than its content).
      But what if we want to use these characters safely as a part of the documents content?
        We need to HTML-encode them

          Exs:
            &quot;
              "
            &apos;
              '
            &amp;
              &
            &lt;
              <
            &gt;
              >

    Additionally, any character can be HTML-encoded using its ASCII code in decimal form (prefixed by &#)
      Ex:
        &#34;
          "

      Or, by using its ASCII code in hexadecimal form (prefixed by &#x)
        Ex:
          &#x22;
            "
