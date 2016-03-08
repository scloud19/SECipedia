Implementation Flaws in Authentication
  Fail-Open Logic Mechanisms
    Ex:
      public Response checkLogin(Session session) {
        try {
          String uname = session.getParameter(“username”);
          String passwd = session.getParameter(“password”);
          User user = db.getUser(uname, passwd);
          if (user == null) {
            // invalid credentials
            session.setMessage(“Login failed. “);
            return doLogin(session);
          } 
        }
        catch (Exception e) {}
        // valid user
        session.setMessage(“Login successful. “);
        return doMainMenu(session);
      }

      If the call the db.getUser() throws an exception for some reason (ex: a null pointer exception from a request not having a username/pass param), the login won't exactly FAIL
        While the resulting session might not be bound to a particular user (and thus not fully functional), an attacker could potentially still gain access to sensitive functionality.

      While an app developer might not make a mistake this silly, this type of problem can exist when multi-stage functionality exists (and/or several layers of method calls)

  Defects in Multistage login mechanisms
    Logic Flaws
      If a user is at stage 2, the app assumes that the user successfully passed stage 1
        This includes validations
          Ex: Maybe the logic mechanism validates if a user has an admin role at phase 1, and then trusts at phase 2 whatever role flag is entered.
            Other validation examples: if the users account has expired, is locked out, etc.
          Ex: The app authenticates in phase 1 the username/pass, and then trusts the username hidden form field in later stages.
      
Secret Question Logic Flaws
  Out of a list of predefined questions that the user has answered, some apps select an entry randomly
    Idea: By doing this, if an attacker witnessed the previous login steps, this element of "randomness" will lock them out

    Flaws
      App may present a randomly chosen question in the UI, but then store the details within a hidden form field/cookie
        Then, the Q/A are both sent to the server
          Obviously an attacker can just submit the Q/A combination that they already recorded
      App presents a randomly chosen question on each login
        But, doesn't remember the LAST question that it gave to the user.
        An attacker can simply go through the login process again and cycle through the questions.
          Usually a user won't invoke an account lockout for this.

Bad Passwords
  Many web apps give no/minimal quality control on user passwords.
    Ex: Some web apps allow passwords that are
      Very short/blank
      Common dictionary words
      Same as the username
      Set to a default value

    Admin passwords might not be subjected to the same stringent guidelines

    Insecure storage
      If passwords are being hashed using a standard algorithm such as MD5/SHA-1
        Allows an attacker to simply look up observed hashes in a rainbow table



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

  Backdoor password
    Sometimes as simple as a ubiquitous password that can be used with any username

  Problems
    As it's a 'hidden' function, it might have insufficient access controls.
      Ex:
       By finding the 'hidden' url
       A cookie such as "SessionOf=user" that can be easily changed.
    If admin users can be impersonated, there could be a huge vulnerability

Password Validation Policies
  Some apps might not display password validation messages to the user
    Instead, they can simply perform their own "sanitation"
      Bad examples
        Only use the first n characters of a password
        performing a case-insensitive check
        Stripping unusual characters

Predictable usernames that can be enumerated
  Ex: user1, user2, etc.

Predictable Initial Passwords
  Some applications generate an initial password for you
    Is it truly random?

    1 Ex: Users are created all at once or in sizable batches
      Any pattern that you can deduce and extrapolate to other users?
        Is it derived from something that can be easily understood? Ex: username, title, etc.
      This is most common in intranet-based corporate apps
        Ex: Where every employee has an acct and gets a notification of her password.

Insecure distribution of creds
  Account activation url
    Usually allows a user to set their own password, etc.

    Look for patterns in this url, or try to enumerate over its values
































