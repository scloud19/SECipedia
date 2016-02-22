Bad Passwords
  Many web apps give no/minimal quality control on user passwords.
    Ex: Some web apps allow passwords that are
      Very short/blank
      Common dictionary words
      Same as the username
      Set to a default value

  Admin passwords might not be subjected to the same stringent guidelines

Brute-Forcible Login
  If a user tries so many logins within a timeframe, deny him/her
    This can be a problem if a corporation is behind an single IP, etc.

  Certain apps may set a cookie "failedlogins=1" and increment it on every failed login.
    After a certain threshold, the server ignores the request.
      Can be easily circumvented

    Sometimes, this information can be held in the session
      All the attacker needs to do is obtain a fresh session
        Ex: by withholding his session cookie

Bad Account Lockouts
  If an account is being Brute Forced and it's thus locked
    Don't still display information that allows a hacker to deduce if the login WOULD be successful.

Verbose Failure Messages
  Dont disclose what portion of the authentication failed.

  Dont even disclose (invalid username)
    Also, hackers can leverage registration forms to see which users exist/dont exist
    A user could then enumerate over common usernames.
    Exploit: password guessing, attacks on user data/sessions, social engineering


