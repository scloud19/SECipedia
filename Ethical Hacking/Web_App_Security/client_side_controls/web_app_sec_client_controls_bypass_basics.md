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

      This is a hidden field that is created by default on all ASP.NET apps

      Contains ser      

