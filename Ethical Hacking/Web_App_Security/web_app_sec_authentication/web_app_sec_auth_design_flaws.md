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

  Multistep auth mechanism
    If the messages at a step are too verbose, this can cause many problems as well.
      The attacker can attack each step in the process

Vulnerable Credential Transmission
  This isn't just limited to username/pass, it can be the sessiontoken, etc.
  Potential eavesdropping locations
    on/in the..
      LAN
      the users ISP
      internet backbone
      within the ISP (IT department) hosting the application

  Vulnerable methods
    Credentials transmitted via query string params
      Should put them in the body of a POST
      These can be logged in the browser history, within the web server logs, and within the logs of any reverse proxies within the hosting infrastructure.

      Switching from HTTP to HTTPS at the exact point of login
        If you did to go down this rough path, one needs to switch to HTTPS  for when the login page is loaded into the browser.
          NOT when the credentials are submitted in the login form.
            Ex: A suitably positioned attacker can intercept and modify the login page, and change the submission url of the form.

Password change functionality
  Many web apps don't provide password changing functionality

  Often are accessible without authentication
    In general, a lot of the care that gets placed in the main login function isn't placed into the password change functionality

  Problems
    Verbose error messages
      Indicate whether the requested user-name is valid

    Unlimited guesses of the existing password field

    Check if the "new password" and "confirm new password" fields have the same value AFTER validating the existing password.
      This allows a non-invasive attack

    Decision tree complexity
      Needs to..
        ID the user, validate the supplied existing password, integrate with any lockout defenses, compare the supplied new passwords with each other and against password quality rules, feed back any error conditions.
          Due to the complexity, there are can subtle logic flaws that compromise the function.

Forgotten Password Functions
  Regain control functionality
    Ex: Sending a user a link to their on file email address
      Secure way
        Send a unique, unguessable, time-limited recovery URL

    Insecure overall methods
      Upon successful response to the challenge question
        Displaying what the old password was
          Now, whenever the correct user changes his password, the attacker can easily get it.

        Not notifying the email address on file

  Similar to password change functionality, devs commonly dont think about brute forcing mitigation on these ares of the app.

  Are often the weakest link in auth mechanisms
    Often give a challenge/response for items like, "favorite color"
      Relative to passwords, this drastically reduces the amount of potential responses that can be used.

  Often leave it up to the user to design the questions
    This questions are often very insecure
  Introduce many of the same problems as the Password change functions.
    Ex: username enumeration

"Remember Me" functionality
  Used so users dont have to reenter their username/pass on every visit.

  Opens up attack vectors on local/remote computers

  Ex:
    Sometimes all the app does is set a cookie with the username
      Ex: RememberUser=user38
        Once this cookie is submitted, the application automatically logs in that user
          Exploit
            All one would need is to 
            enumerate usernames
      Even if the cookie is encrypted, others can still steal it through XSS or access to local ENV.

User impersonation features
  Usual context
    Given to helpdesk individuals so they can switch into a customers account.

  Problems
    As it's a 'hidden' function, it might have insufficient access controls.
      Ex:
       By finding the 'hidden' url
       A cookie such as "SessionOf=user" that can be easily changed.






























