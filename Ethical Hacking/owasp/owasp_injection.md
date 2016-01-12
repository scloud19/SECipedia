Injection
  The number one risk
    We'll focus on mySQL injection (as it's a huge injection risk), however you can abstract these concepts to other forms of technologies as well (ldap injection, server side include (SSI) injection, etc.)

    mySQL injection overview
      Consists of insertion (aka "injection") of a SQL query via the input data that travels from the client to the server.
        This payload is delivered by a simple HTTP request.
        
      Specifically, owasps definition:
        SQL injection occurs when:
          1) Data enters a program from an untrusted source
          2) The data is used to dynamically construct a SQL query

          We'll touch on these two areas more as we move throughout the lecture.

      Difficulty
        For Blackhat
          Easy
            Many automated tools.  (Give examples)
              Just paste a website url

        For sysadmin
          Not difficult, but not easier either.  Need to probe logs for certain injection strings that can be highly creative.
            Thus, for some injection patterns, you can't simply write your own regex to detect the pattern.

      Common areas of occurrence
        Everywhere, but especially common in PHP and ASP apps due to the prevalence of "older" programming interfaces that are still in use.
          Ex: Wordpress has a high level of vulnerability


      Example attack surface
        1) Certain SQL statements (on certain databases) can access the server's CLI.

          Ex: xp_cmdshell on Microsoft's SQL Server 2014

            EXEC xp_cmdshell 'dir *.exe';
            GO

          This will return a listing of all files
            Obviously, it can get much worse than this but the commands that are issued have the same privilege level as the server

          This is turned off by default, but it wasn't always that way.  Thus there are vulnerable systems out there.

        2) Reading sensitive data from the database (SSNs)
        3) Modifying data in the database (account balances)
        4) Executing administrative operations on the database (shutting it down)


      
  Ex: mySQL injection
    Ex: What the developer is expecting
      
      Ex: The request that the developer is expecting
      http://www.snuggie4cats.com/products?id=1,type=products
      
      Ex: The code that the developer wrote, based on his/her expectation.  Do you see any weaknesses?

      // Connect to db
      Connection dbconnection = DriverManager.getConnection(dbUrl,username, password);

      // Grab the params
      String table = request.getParameter("type");
      int id = Integer.parseInt(request.getParameter("id"));

      
      String query = "SELECT * FROM " + table + " WHERE ID = " + id;

      // SELECT * FROM (TABLE) WHERE ID = (NUMBER)

      // create our query 'holder'
      Statement selectStatement = connection.createStatement();

      selectStatement.executeQuery(query);

        Probs: Concatenation, no data validation (min/max length, permitted characters)
        
      // Make sure that I have colons everywhere in code

      (Later speak about how to use parameters and prepared statements http://javarevisited.blogspot.com/2012/03/why-use-preparedstatement-in-java-jdbc.html)


    Attacker -> Website -> Database
    
    1) Attacker forms an malicious HTTP request to the db
        The malicious part of the request might be inside the query string, etc.

        Ex: Attack 
          http://www.snuggie4cats.com/users?Id=null OR 'a'='a'
            
            Which can translate into the SQL string...

              SELECT * FROM users WHERE ID = null OR 'a'='a'
                
                The addition of the "OR 'a'='a'" makes the WHERE clause always evaluate to true, so if this input isn't properly sanitized, this will run:
                  SELECT * FROM users;

    2) Website does a transformation on this HTTP request, and makes it a query to the database (to get relevant information associated with the request)

      Although, the adulterated request's goal is to change the behavior of this step; so that the webserver's query to the database changes (and does an operation that it wasn't supposed to do)
        Ex: Instead of querying the products in a table (which was allowed), change the query so it returns all of the information in the users table (ex: passwords, addresses, etc.)

    3) If this operation was successful, the query results are passed to the web server

    4) Web server passes this information back to a user which in many cases, can even be shown in the user's browser

    Types of SQL injection: (give examples).
      Some techniques are so sophisticated that an attacker can glean information on how the attacks working by just looking at how LONG the server takes to respond to different injection attacks.
        (Time based attacks?)

    Attack mitigation
      These are some of the most common, remember this is NOT an exhaustive list

      1) Realize trusted/untrusted data
        Ex from above:

        http://www.snuggie4cats.com/users?Id=1
          
          If a SQL statement that's being generated is.
            SELECT * FROM users WHERE ID = 1

            AND

            Assuming that snuggie4cats.com has no other domains/subdomains
              Trusted: http://www.snuggie4cats.com
              
              Untrusted: Anything after the .com

            Thus, we need to whitelist the tables that can be queried from a non-admin; ex: so a user couldn't try to navigate to http://www.snuggie4cats.com/users which given our current code, could produce:
              SELECT * FROM users

              Even if it isn't immediately apparent that your code would construct a SQL statement like the following; you ALWAYS whitelist your table access for non-admins.


            We also need to whitelist the types of query strings that are allowed, i.e. (so there wont be 'Id=1 or 'a'='a'')
              Ex: http://www.snuggie4cats.com/products?Id=1 or 'a'='a'

              Brainstorm for whitelisting
                Ex: Only accept integers that are positive and within the bounds of product ID range.

                So, in other words, what is the most restrictive way that we can lock down a query.

                If this whitelisting was to occur, the attack query would fail right off of the bat because it isn't a integer

                Caveat:  All query strings are delivered to the server as a string.  You need to run a check that this string ONLY contains integers.  It would be helpful to make sure that whatever library you're utilizing doesn't do this type conversion in a way that is unexpected.

        2) Parameters
          Is about separating the sql query from the untrusted data.



