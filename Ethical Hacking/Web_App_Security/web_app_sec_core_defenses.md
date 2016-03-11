Trust boundary
  There can't be a complete trust boundary between the internet's input into the server and the servers input into the application.  It is impossible to effectively sanitize everything at one layer (given the complexity of attacks).

    Boundary Validation
      A more effective model where each individual component of functional unit of the server-side application treats its input as coming from a potentially malicious source.
        Ex: WHH-27

        Sanitize based on the component (i.e. what the potential exploits can be for that functional unit)
        
        Data validation is performed at each of these trust boundaries, in addition to the external frontier between the client and server.
          It is essential to do it at every unique layer (as data from one layer can be mutated/transformed before reaching another layer)

          Do data validation both client side and server side.



Handling Attackers
  Spidering
    Make your urls have an element of randomness to them
      As spiders usually won't traverse the same link twice, This can cause spiders to go into an infinite loop.

    Use per-page tokens
      The spider will probably fail to handle these properly by requesting pages out of their expected sequence.    
        If the web app is aware of this, it can defensively terminate the session.

    For enumerating hidden content, attackers utilize common path names that are stored in publicly available "master files".
      When making paths, make sure that they aren't within these "master files" aka /admin routes, etc.



  Handling Errors
    The application should never return any system-generated messages or other debug information in its responses.
  Keeping logs
    The IDS should be aware of any client side input validations, if these are being circumvented, the user is known to be malicious
    
    At a minimum:
      All events relating to the authentication system
        Successful and failed login
        Change of password
      Key Transactions
        CC payments
        Fund Transfers
      Access attempts that are block by the access control mechanisms
      Any requests containing known attack strings

      Identifier portion of the log entry.
        Time
        IP
        User's account

    Log Security
      Strong protection against read/write access
      Should only allow update actions from the main application
      Flush logs to write-once media to ensure their integrity in the event of a successful attack.


  Alerting admins
    Some IDS/WAFs include these features in this section.
      But remember, these mitigate more generic attacks and aren't specific to application logic/business logic.
        In general, the most effective way to implement real-time alerting is to integrate the alerting functions with the applications input validations (and other controls.)
          Ex: Check the contents of the cookie string, is it abnormal given your logic?
            This validation should already be happening, thus do alerting functions based off of it.
              This reduces false positives

    Reduce false positives by looking for
      Usage abnormalities
        Large number of requests from a single IP or user (indicates a scripted attack)
          Need to balance this with large subnets using 1 IP.

      Business abnormalities
        Unusual number of funds transfers being made to/from a single bank account

      Requests with known attack payloads

      Requests where data that is hidden from ordinary users has been modified (hidden form field, etc.)


  Reacting to attacks
      Due to the differences in web apps, most attackers need to probe multiple times for vulnerabilities.
        Many input validation mechanisms will identify these requests as malicious and block the input.
          The key is, assume that something is going to "slip by".  Thus, you need to do items to frustrate a "flagged" user
            Exs to "by time":
              Respond to attackers requests increasingly slowly
              Terminate the attacker's session

Securing Authentication
  Strong Creds
    Suitable minimum password quality requirements should be enforced.
      Ex: Enforcing a...
        (Note: you must look at the cost/benefit with usability with these)
        Min length
        Max password time
        Alphabetic, numeric, !@ type symbols
        Appearance of both uppercase/lowercase chars
        avoidance of dictionary words, names, common passwords, user names in passwords
        password uniqueness policy (on password change)
          As with most security measures, one can set set password requirements depending on privs

        Any system-generated usernames/pass' should be created with sufficient randomness

    Handle Creds Secretively
      Always utilize SSL
      If using HTTP
        Always have the login form itself loaded via HTTPS
          DONT switch to HTTPS at the point of login submission
      A random salt should be generated for EACH password
      "Remember me" functions
        Should remember only non-secret items such as usernames
          For a non security critical app, potentially allow users an ability to "auto login"

    If you're generating a password and sending it to a user (email, text, etc.) it should be time limited
      The user should be required to change the password on first login and told to destroy the previous communication after first use
    Capture certain parts of a users login via drop-down menus rather than text fields
      This will prevent SIMPLE keyloggers from capturing all the data the user submits

    Validating Creds Properly
      The app should use a catch-all exception handlers around all API calls.
        These should delete all session and method-local data being used to control the state of the login process
        These should also invalidate the current session
          This forces a user to log out even if auth is somehow bypassed

        Do not allow user impersonation features on public facing servers

      Multistage logins
        All data about progress through the stages and the results of previous validations (i.e. did Stage 1 successfully pass) should NEVER be stored or interface with the client

        No information should be submitted more than once by the user
          There should be no way that the user can modify data that was already collected and/or validated
            Ex: Even a username shouldn't be submitted multiple times.  This should be set once on a server session object and referenced from there (on the server).

        The first task at every stage should be to verify that the prior stages have been correctly completed.
          If not, stop the auth process

        Never disclose which stage of the login failed
          The application should aways proceed through all stages of the login
            even if the user submitted ANY invalid input
              including an invalid username
          If the login failed, just have a generic "login failed" message

        Randomly varying question
          Used so someone who observed the users input couldn't replay the login sequence

          For multistage logins, make sure the users ID themselves in an initial stage and the random question is presented at a later stage
            Thus, you can store the question in a serverside object to make sure that the user is presented with the SAME question until they answer it successfully.
              If not, one could easily cycle through the questions on subsequent logins


              For multistage logins, it's recommended to let a user proceed through the entire sequence (even for invalid input)
                However, as the question isn't directly attached to a specific user, an attacker could infer if a user is valid or not (the question will vary)
                  This can be leveraged for username enumeration.

          Store the question that has been asked on the server.
            If it was on the client, the user could simply submit the question/answer combo that they want.

        Preventing Information Leakage
          Should not expose any info about auth params
            The attacker should have no means of determining which piece of the items submitted caused a problem
          A single code component should be responsible to responding to all failed login attempts  
            Otherwise, subtle changes in the response patterns can tip off an attacker 
            It should have a generic message

          Don't display definitively whether a username has been locked out.
            This could lead to an enumeration risk
            Keep a generic message on the ubiquitous error page that states, "An account can be locked out if a user attempts many valided login attempts, if you feel that this has occurred, please try to login later"

          Mitigating username enumeration on self-registration forms
            Use email addresses as usernames
              Ex: Registration Process
                1) Prompt for an email address and instruct the user to wait for an email to arrive containing instructions
                  Instructions for email
                    If the email is already registered, the email that is sent will specify this
                    If the email is unique, give a random URL to visit to continue the registration process

          Preventing Brute Force Attacks
            Common areas that need to be protected
              login form, forgotten password, etc.
            Disable an account for 30 mins upon 3 unsuccessful logins in succession
              This mitigates DOS attacks
              When an account is in an disabled form, a login should be rejected without even checking the password

            Add CAPTCHA to every page that could be brute forceable

          Misuse of the Password change function
            There should ALWAYS be a password change function
            Should only be accessible inside of an authenticated session
            Should be client-side way to feed a username into the function
            Users should be forced to reenter their existing password before the change occurs
            The new password should be entered twice

            Error handling
              1 single generic error message

            The function should temporarily suspend following a small number of failed attempts

            Users should be notified via an alternative means (email,etc.) that their password has been changed.


          Preventing Misuse of the Account Recovery Function
            Security Critical Apps
              Account recovery