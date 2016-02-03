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
