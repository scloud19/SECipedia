Browser Extensions
  Outside of HTML forms, the other main method of validating/submitting user data.

  See browser_extensions.md

Traditional Implementations
  Applications passing data to the client in a form that the end user can't directly see/modify
    The expectation is that this data will be sent back to the server in a subsequent request.


Flaws in developer implementation
  Many times, developers make the assumption that ordinary users can't modify these URLs and their params
    That load embedded images
    Load a frame's contents
    A POST url
    Where an app uses pop-up windows (or other techniques) to conceal the location bar


Referer Header
  See HTTP_headers.md

  Developers can use the referer header to make sure that requests originated from the "correct" page.
    Ex: Think of password reset flows

    This can obviously be exploited

Opaque Data
  Sometimes data on the client is encrypted or obfuscated in some way.

  Usually, you'll see this as a part of the apps session-handling mechanism
    Ex: Session tokens sent in cookies, anti-CSRF tokens in hidden fields, one time URL tokens for accessing app resources.

    Or, this helps with tracking the state of the view (offloads processing from the server)
      This enables the server to preserve elements within the UI across successive requests without needing to maintain all of the relevant state info server side 
      Ex: ASP.NET viewstate

  Ex: <input type=”hidden” name=”pricing_token” value=”E76D213D291B8F216D694A34383150265C989229”>

  Once you see this type of data, you know that something is juicy (as something is trying to be hidden.)


  Attacks:
    If you know the plaintext behind the opaque data, you can attempt to decipher the algorithm.

    You the app may contain functions elsewhere that you can leverage to return the opaque string (from a piece of plaintext input that you control).

    Replay attack
      If you can't penetrate the obfuscated data, it may be possible to replay its value in other contexts to achieve a malicious effect

      Ex:
        <input type=”hidden” name=”pricing_token” value=”E76D213D291B8F216D694A34383150265C989229”>

          What's if you inject that obfuscated value into other more "expensive" inputs?


    If all else fails, attack the server-side logic that will decrypt the string by submitting malformed variations of it
      Ex: overlong values, different char sets, etc.

    ASP.NET ViewState
      A common mechanism for transmitting opaque data via the client is the ASP.NET viewstate

      By default, ASP.NET protects the ViewState from adulteration by adding a keyed hash to it (known as MAC protection)
        Note: This can occur on a per-page basis, so some pages might not be protected.
          If you are using Burp Scanner (with passive scanning enabled), Burp automatically reports any pages that aren't MAC protected.
        Burp has a viewstate parser that indicates whether the viewstate is MAC protected.
          If it isn't, there is an editor in Burp that you can change the values.
            Try modifying a value (without changing the overall structure), and see if an error message results.

          If it is protected, you can still decipher the viewstate in burp to see if there is any sensitive data.
        MACs are basically cryptographic checksums. They are used to detect when an attacker has tampered with a message. Therefore they require a secret key (to be withheld from an attacker) and should be as fast as possible (to reduce overhead).

        Remember, this doesn't prevent replay attacks.

        Although, some applications disable this default protection (so you can modify the viewstate's value)

      This is a hidden field that is created by default on all ASP.NET apps

      Contains serialized information about the state of the current page. 

      Developers use this to store arbitrary information across successive requests.
        Ex: Can also set pricing information
          <form method=”post” action=”Shop.aspx?prod=3”>
            <input type=”hidden” name=”__VIEWSTATE” id=”__VIEWSTATE” value=”/wEPDwULLTE1ODcxNjkwNjIPFgIeBXByaWNlBQMzOTlkZA==” />
            Product: HTC Avalanche <br/>
            Price: 399 <br/>
            Quantity: <input type=”text” name=”quantity”> (Maximum quantity is 50) <br/>
            <input type=”submit” value=”Buy”>
          </form>

            Ex: POST body
              __VIEWSTATE=%2FwEPDwULLTE1ODcxNjkwNjIPFgIeBXByaWNlBQMzOTlkZA%3D%3D&quantity=1

              ViewState param is Base-64 string that can be decoded
                See decoding.md


Form Data
  Scan the forms an see if the developers are placing any validations/limits on user input.  If so, they might be assuming that the input is safe on the server.
    If you are trying to inject malicious input into multiple fields
      Only try 1 malicious input at a time (while the rest are normal).  On the server, sometimes it will stop processing on the first condition that it feels is "abnormal".  


  Script Based Form Validation
    For custom validation solutions (outside of HTML 5 form validations), you can see something like
      <form method=”post” action=”Shop.aspx?prod=2” onsubmit=”return validateForm(this)”>

      Very simple to get around this
        The most elegant way
          Enter a normal value and then intercept the request and then change the value.

  Disabled inputs
    <input disabled='true'>

    Aren't submitted as a parameter in the form.
      Thus, to find these, you need to look through the applications source.

      Burps HTML modification feature
        Will automatically re-enable any disabled fields within the app.

    Sometimes this means that the input was used for testing, etc.  If you see a disabled input, try submitting that parameter and see if the server still accepts it.

    Disabled Submits
      Submit elements are flagged as disabled so that buttons appear as "greyed out" in contexts when the relevant action is unavailable.

      Always try to submit the names of these elements to see whether the app performs a server-side check before attempting to carry out the requested action


