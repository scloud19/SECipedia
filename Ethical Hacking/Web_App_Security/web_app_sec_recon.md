EH - Ethical Hacking

In EH in general, the most effective approach is to use manual techniques that are supplemented by controlled automation.

In summation
  It is tempting to try to find bugs "off the bat", but dont do this.  You need to do your recon

  When doing your recon. Leverage:
    Manual browsing and user-directed spidering to enumerate the apps visible content and functionality

     Brute force (combined with human logic) to discover as much hidden content as possible.

     Assessment of the app's attack surface 
       An analysis of the app to ID its key functionality, behavior, security mechanisms, and technology.





Strategic Areas of Attack
  Aka: Where do I get the biggest bang for my buck?

  If an app is using a well-known framework that prevents XSS, SQL injection, etc. an attacker shouldn't attack these portions.  They should look for areas that have been "added on" and maybe aren't handled by the general security framework.

  "Added on" areas
    Can be found by
      Differences in GUI appearance
      Param naming conventions
      Comments in the source code
      Differences in navigation mechanisms

      Look for functionality that is likely to be added "later"
        CAPTCHA controls
        Usage tracking
        Third-party code

Dissecting Requests
  
  Ex1
  https://domain.com/calendar.jsp?name=new%20applicants&isExpired=0&startDate=22%2F09%2F2010&endDate=22%2F03%2F2011&OrderBy=name

    Items to note
      .jsp - Java server pages
      OrderBy - maybe an ORDER BY clause of a SQL query
      isExpired - BOOLEAN.  Maybe an access control vulnerability? This could allow a user to inspect expired content

  Ex2
  https://domain.com/workbench.aspx?template=NewBranch.tpl&loc=/default&ver=2.31&edit=false

    Items to note
      .aspx - This is a ASP.NET app
      template - Probably used to specify a filename (file ext= .tpl)
      loc - directory name
      edit - obvious


      Maybe this route is vulnerable to path traversal attacks?

  Ex3 (Request used to submit a question to app admins)

    POST /feedback.php HTTP/1.1
    Host: wahh-app.comContent-Length: 389

    from=user@domain.com&to=helpdesk@domain.com&subject=Problem+logging+in&message=Please+help...

    Items to note
      .php

      Looks like the server is interfacing with an email server, thus could be vulnerable to e-mail header injection


    Ex4
      http://eis/pub/media/117/view

        Items to note
          Potential possibilities
            source 117 is probably in the collection "media"
              Change media to "users", "pages", etc.

            App is performing an action on this resourve that is equiv to 'view'
              Potentially change to - edit or add
                add
                  But add would likely fail because a resouce of 117 already exists
                    Try to add 118 or a very high number


              Look into the structure of other routes to confirm if these assumptions are true (or mess with the params)

        is probably equiv to

          http://eis/manager?schema=pub&type=media&id=117&action=view


Extrapolating App Behavior
  Ex: You find a potential SQL injection vulnerability but you don't have the ability to see how the input is sanitized, etc.
    Look for a portion of the application that echos back user input.  As input sensitization (and other items) are often global in scope, there's a high probability that code is being reused.
      Given this echoed output: try different encodings and strings to see what happens.

  Ex: You are given a obfuscated cookie and need to find what it means?
    Some apps use custom obfuscation schemes (when storing data on the client) which can be very difficult to decipher.

    Although, there may be situations where a user can submit an obfuscated string that gets "reversed" to the original.
      Ex: An error message may include the deobfuscated data that led to the error.
        Thus you could reverse engineer the obfuscation scheme


  Ex: Error handling is often handled inconsistently throughout the app.
    Some areas might handle errors gracefully, while others simply crash and return verbose debugging to the user
      Utilize the errors in certain parts of the app to deduce components that can't be readily "seen" by the areas that handle errors gracefully.


IDing Server-Side Technologies
  Remember, items such as the Server header, etc. can be falsified by the sysadmin.

  Banner Grabbing
    Server header
      Remember that different portions of the app may use different servers, so different server headers may be received depending on the different part of the app that is utilized.

    Additionally, you may find server info in
      Custom HTTP headers
      Templates used to build HTML pages
      query string params

  File Extensions
    asp - Microsoft Active Server Pages
    aspx - Microsoft ASP.NET
    jsp - Java Server Pages
    cfm - Cold Fusion
    php - Php
    d2w - WebSphere
    pl - Perl
    py - Python
    dll - Compiled native code (C/C++)
    nsf/ntf - Lotus Domino

  URL Signatures Mappings
    Vignette CMS
      https://wahh-app/news/0,,2-421206,00.html
        Notice the comma-separated numbers

  Directory Name mappings
    servlet - Java servlet
    pls - Oracle Application Server PL/SQL gateway
    cfdocs/cfide - Cold Fusion
    SilverStream - SilverStream web server
    WebObjects/ {function_name}.woa - Apple WebObjects
    rails - Ruby on Rails

  Session Token Mappings
    JSESSIONID - Java Platform
    ASPSESSIONID - Microsoft IIS Server
    ASP.NET_SessionId - ASP.NET
    CFID/CFTOKEN - Cold Fusion
    PHPSESSID - PHP




    To see if a web server is of a particular type, try requesting an invalid file with a given file extension (ex aspx)
      If doing yo.com/doesntexist.html
        Returns generic "not found" message
      If doing yo.com/doesntexist.aspx
        Get a ASP.NET error message


      This occurs because many web servers map specific files extensions to different executables on the server
        Each component may handle errors (or '404s') differently


Web Server Vulnerabilities
  Look at the server layer for vulnerabilities that could allow for directories to be exposed

Spidering
  Done through a tool

  Following all links in a website and figuring out the underlying site structure

  Tools: Netsparker


Forced Browsing
  Will try to hit urls that aren't found through traditional spidering

  Tools: Burp Suite

HTML Source
  By looking at the HTML source, you can often view the validations that are occurring.
    Ex: All passwords need to have a max-length of 10 chars and can only be alphanumeric.
      This can easily inform a brute force attack

  HTML source also has a lot of other goodies, so it's important to throughly review it.
    Ex:
      The backend architecture of the app by reviewing the URL structures, etc.

Using Public Information
  An app that you're surveying may contain content and functionality that is not presently linked from the main content but has been linked in the past.
    

    To find this information
      Search Engines
        Exs: Google, Yahoo, MSN

        Also look for cached copies of the content

        View google_hacking for more info.

      Web Archives
        Ex: WayBack Machine (archive.org)

      Internet Forums
        Ex: stackoverflow.com,etc.

        Look up developers from a company on linkedin and then look for those developers on stackoverflow, etc.
          Additional ways to find people
            Names within the HTML source code, names found in the contact information section of the main website

        Usually, these forum posts contain source code, technologies in use, etc.


      These repos also contain references to content that is linked from third-party sites, but isn't immediately visible in the target application itself.
        Ex: Some applications contain restricted functionality for use by their business partners.  These partners may disclose the existence of the functionality in] ways that the application itself does not.

      Technique

Analyzing the Application
  Keys areas
    Core functionality
      The actions that can be used (when the app is used as intended)

    Peripheral app behavior
      Off-site links, error messages, admin/logging functions, use of redirects.

    Core security mechanisms
      Management of session state
      access controls
        "Remember me" functionality
          Invoke this functionality and then observe the results in cookies/localstorage.
            Does the app simply set a "RememberUser=user73" cookie and blindly trust the client side application?
            Obfuscation
              Sometimes user73 can be a number that the application maps to a user.
                Can you enumerate over this number? Are there any patterns in the mapping?
              Create many users that are very similar in username and/or password, etc.  Does any of the obfuscated data have similarities?
      auth mechanisms/supporting logic
        Ex: user registration, password change, acct. recovery

        Forgot my password functions
          See if it's vulnerable to brute forcing

          Obtain a bunch of different account recovery URLs (usually come to the account owners email)
            See if there's any patterns in the URL structure.

          Also see 'password change' steps below for inspiration

          Where to send the new password or activation link
            See if the users email is stored in cookies/(hidden form field)
              Double opportunity
                Discover the compromised users' email

                Modify the value to go to your email



        Password change
          1) ID any password change functionality within the app.  If it's not readily visible, it still may be there.

          2) Make requests to the password change function using invalid usernames, invalid existing passwords, and mismatched "new password" and "confirm new password" values.
            Get a valid username and see if the password authentication mechanism requires valid passwords to change passwords.  Study the responses for valid usernames.

            See if the password change field is available to unauthenticated users.

            If the password change field doesn't allow username submissions (on the surface)
              look for hidden form field submissions.
              Try submitting a username with the parameter name that's leveraged on the main login forms.

          3) ID any behavior that can be leveraged for username enumeration or brute-force attacks



    User input
      All of the different locations at which the app processes user-supplied input
        Ex: every URL, query string param, item of POST data, cookies, HTTP headers
          Headers of importance
            User-Agent, Referer, Accept, Accept-Language, Host

    Tech employed on the server side (including static/dynamic pages), types of req. employed, use of SSL, interaction with databases (interaction with email systems, and other backend components.)

Mapping the surface for Attack
  aka "What vulnerabilities should I look for at every level of the app?"

  Client-side validation
    Checks may not be on the server
  Database interaction
    SQLi
  File interaction (uploading/downloading)
    Path traversal vulnerabilities, persistent XSS
  Display of user-supplied data
    XSS
  Dynamic redirects
    Redirection and header injection attacks
  Social networking features
    username enumeration, stored XSS
  login
    Username enumeration
      Look in source code for username ideas, also look at employee emails
        One can try the whole email or just the username portion of it.
      Leverage registration forms to see which users exist/dont exist
      Any time a user can make their own username, there's an enumeration risk.

      If one is testing an email service provider, are the user IDs simply emails addresses? If so, simply search for google for @domain.com

      Are the userIDs enumerable by design?
        Ex: user1, user2, etc.? OR any type of auto-generated username
          If so, try to create several users in quick succession to see if there's a more granular pattern that you can deduce
      Look at timing differences between correct/incorrect usernames. 

      Do a DIFF on the exact payload differences between correct/incorrect username submissions (with a wrong password)
        Sometimes, there's incredibly small nuances that can tip off an attacker.
        Leverage the comparer tool within Burp to automatically highlight the differences between 2 app responses

      Steps
        1)Submit a request for a valid username (with an incorrect pass) and an invalid username
        
        2) Record every detail of the servers response
          Ex: status code, any redirect, basically everything in the HTTP response is in SCOPE

        3) Do a comparison using a tool

        4) If you can't find any changes, try it on self-registration, password change, forgotten password, etc.

        5) If a difference IS detected in the server's response, leverage a list of common usernames, and deduce which one/s are valid.
          a) Before doing this, see if there is any account lockout mechanisms.
            Ex: If there is, you just "wasted" one attempt by trying to find the username (if found.) Thus, try all usernames with a common password, (ex: password1 or the username itself)
              BURP: To make the login/password the same, leverage the "battering ram" attack mode.

    weak passwords, brute forcing
    Brute Forcing
      First, how does the app respond to successful attempts?  Then, deduce how it responds to unsuccessful attempts
        With this baseline, start your brute force.

  Passwords
    Attempt to discover any rules regarding password quality
      Either by creating accounts or looking for rules on the website
      If you can create an account, try changing its value to a weak one.

      Some apps might not display password validation messages to the user
        Instead, they can simply perform their own "sanitation"
          Bad examples
            Only use the first n characters of a password
              Try removing the last character and see if you can still login
            performing a case-insensitive check
            Stripping unusual characters
          Have a valid username/pass and see if differences in the password still allow you to log in.
            This can greatly reduce the amount of brute forcing
    Attempt to login with a bad password 10 times
      If you control the account, attempt to login.
        If successful, there's probably no account lockout policy

    Predictable initial passwords
      If the app generates passwords, try to obtain several in quick succession
        Any pattern?
          If so, extrapolate and utilize in a brute-forcing attack with usernames
  Multistage login
    Logic Flaws
  Session state
    predictable tokens, insecure handling of tokens
  access controls
    horizontal/vertical privilege escalation
  user impersonation functions
    privilege escalation
    When bruteforcing passwords, you might get a situation where there are 2 "hits"
      One of the passwords would likely be a backdoor that is implemented for user impersonation functions (for use with helpdesk, etc.)
        Usually, this password is the same across all accounts

        Reviewing passwords that have been brute-forced for a site
          See if there are cases that multiple usernames have the same password
    Look for cases where a username, etc are sent outside of the normal login process.
      Try tweaking this value to another username and see if you can impersonate him
        If you can, try impersonating an admin username
  Using cleartext communications
    Session hijacking, capture of credentials, etc.
  Off-site links
    Leakage of query string params in the Referer header
  Interfaces to external systems
    Shortcuts in the handling of sessions/access controls
  Error messages
    Information leakage
  E-mail interaction
    Email/command injection
  Native code components/interaction
    Buffer overflows
  Use of third-party app components
    Known vulnerabilities
  Known web server software
    Common config weakness, known software bugs
  
  Distribution of credentials
    Activation links
      1)If you're not required to set all creds during registration, find out how the app distributes these items to new user
      2) If an acct activation URL is leveraged
        Register several accounts in quick succession and see if any pattern can be deduced
          if TRUE: predict future URLs and take ownership of accts
      3) Try to reuse a single activation link many times
            if true: You can potentially reset a current users password
              Thus you can extrapolate the activation link pattern BACK in time (as well as FORWARDS) and pawn accts
            if false: try to lock out an acct (many false passwords) and then use the activation link





