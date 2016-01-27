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

    Reduce false positives by looking for
      Usage abnormalities
        Large number of requests from a single IP or user (indicates a scripted attack)
          Need to balance this with large subnets using 1 IP.

      Business abnormalities
        Unusual number of funds transfers being made to/from a single bank account

      Requests with known attack payloads

      Requests where data that is hidden from ordinary users has been modified (hidden form field, etc.)


  Reacting to attacks




