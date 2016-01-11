Injection
  The number one risk

  (Start with example then talk about what injection is)

  Ex: mySQL injection
    Attacker -> Website -> Database
    
    1) Attacker forms an malicious HTTP request to the db
      Anyone can make these requests

      Ex: http://www.injectmebaby.com/products?Id=1
        Can translate into the SQL string
          SELECT * FROM products WHERE ID = 1

      The malicious part of the request might be inside the query string, etc.

      So what's if we do this?
        Ex: http://www.injectmebaby.com/users?Id=null or 1=1
                Can translate into the SQL string
                  SELECT * FROM users WHERE ID = null or 1=1
                    (Explain this later and make sure this query string always does the sql injection by evaluating to true)



    2) Website does a transformation on this HTTP request, and makes it a query to the database (to get relevant information associated with the request)

      Although, the adulterated request's goal is to change the behavior of this step; so that the webserver's query to the database changes (and does an operation that it wasn't supposed to do)
        Ex: Instead of querying the products in a table (which was allowed), change the query so it returns all of the information in the users table (ex: passwords, addresses, etc.)

    3) If this operation was successful, the query results are passed to the web server

    4) Web server passes this information back to a user which in many cases, can even be shown in the user's browser

    Types of SQL injection: (give examples).
      Some techniques are so sophisticated that an attacker can glean information on how the attacks working by just looking at how LONG the server takes to respond to different injection attacks.
        (Time based attacks?)

    Attack mitigation
      Realize trusted/untrusted data
        Ex from above:

        http://www.injectmebaby.com/products?Id=1
          
          If a SQL statement that's being generated is.
            SELECT * FROM products WHERE ID = 1

            AND

            Assuming that injectmebaby.com has no other domains/subdomains
              Trusted: http://www.injectmebaby.com
              
              Untrusted: Anything after the .com

            Thus, we need to whitelist the tables that can be queried from a non-admin; ex: so a user couldn't try to navigate to http://www.injectmebaby.com/users which would produce
              SELECT * FROM users



  Isn't always SQL Injection
    Give examples of other types of injections (ldap, etc.)
      (Go to top ten cheat sheet)

    Although sql injection is very common because of the popularity of mySQL databases in the typical web stack
    
  Difficulty
    For Blackhat
      Easy
        Many automated tools.  (Give examples)
          Just paste a website url

    For sysadmin
      Not difficult, but not easier either.  Need to probe logs for certain injection strings that can be highly creative.
        Thus, for some injection patterns, you can't simply write your own regex to detect the pattern.

  Impact
    Severe
      As many injections can target the database (sql injection)
        (Has a lot of PII) 