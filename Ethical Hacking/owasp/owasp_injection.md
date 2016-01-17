Injection
  The number one risk
    We'll focus on SQL injection (as it's a huge injection risk), however you can abstract these concepts to other forms of technologies as well (ldap injection, server side include (SSI) injection, etc.)

    SQL injection example
      Instead of inputting a normal value in a url's query string, a malicious user inserts (aka "injects") a SQL query instead.
        
      Specifically, owasps definition:
        SQL injection occurs when:
          1) Data enters a program from an untrusted source
          2) The data is used to dynamically construct a SQL query

          We'll touch on these two areas more as we move throughout the lecture.

      It can get very interesting/creative
        Time based attacks
          In any scenario, error messages are an attacker's dream.  However, if they are turned off, attackers can get very creative.
            Ex:  In this case, an attacker can inject certain SQL statements that if true, can pause the execution of the statement (Ex: WAITFOR DELAY '00:00:05';) and then utilize tools to see how long the database responds.  Based on the timing of the responses, an attacker can deduce information about the inner-workings of the database.

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


      
  Ex: SQL injection
    Ex: What the the developer is expecting
example
Instead of inputting a normal value in a form field, url's query string user inserts www.snuggie4cats.s/products?id,type=products i
      Ex: The code that the developer wrote, based on his/her expectation.  Do you see any weaknesses?

      // Connect to db
      Connection dbconnection = DriverManager.getConnection(dbUrl,username, password);

      // Grab the params
      String table = request.getParameter("type");
      int id = Integer.parseInt(
          In any scenario, error messages are an attacker's dream.  However, if they are turned off, attackers can get very creative.
          request.getParametr("id"));
  I inject certain SQL () statements and then utilize tools to see how long the database responds.  Based on the timing of the responses, an attacker can deduce information about the inner-workings of the database.
      // SELECT * FROM (TABLE) WHERE ID = (NUMBER)

      // create our query 'holder'
      Statement selectStatement = connection.createStatement();

      selectStatement.executeQuery(query);


        What problems do you see with this?
          Probs: Concatenation, no whitelisting of params.

        Additional questions to wonder: A attacker could modify the request to access different tables.  Do we have sensitive tables that should be locked down from non-admins (principle of least privilege).
        
      // Make sure that I have colons everywhere in code

      (Later speak about how to use parameters and prepared statements http://javarevisited.blogspot.com/2012/03/why-use-preparedstatement-in-java-jdbc.html)


    Attacker -> Website -> Database
      Give overarching explanation first and then go into specifics below

    1) Attacker forms an malicious HTTP request to the db
        The malicious part of the request might be inside the query string, etc.

        Ex: Attack 
          http://www.snuggie4cats.com/users?Id=null OR 'a'='a'
            
            Which can translate into the SQL string...

              SELECT * FROM users WHERE ID = null OR 'a'='a'
                
                The addition of the "OR 'a'='a'" makes the WHERE clause always evaluate to true, so if this input isn't properly sanitized, this will run:
                  SELECT * FROM users;

          How could we mitigate against this? We'll go over this later, but it is good to start questioning right off the bat.  
            Write down your answers and then compare it my answers at the end.
              This is the only way to really make it stick.

    2) Website does a transformation on this HTTP request, and makes it a query to the database (to get relevant information associated with the request)

      In this example, it runs: 
        SELECT * FROM users;


    3) If this operation was successful, the query results are passed back to the web server

    4) Web server passes this information back to a user; which in many cases, can be shown right in the browser's window.

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



