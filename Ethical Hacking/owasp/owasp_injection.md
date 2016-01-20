Injection
  The number one risk
    We'll focus on SQL Injection (SQLi), as it's a predominant injection risk.  However, you can abstract these concepts to other forms of technologies as well (ldap injection, server side include (SSI) injection, etc.)

    SQL injection example

      www.snuggie4cats.com/products?id=1

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
            Just paste a website url into an automated tool
              Ex: SQL Map

        For sysadmin
          Not difficult, but not easier either.  Need to probe logs for certain injection strings that can be highly creative.
            Thus, for some injection patterns, you can't simply write your own regex to detect the pattern.

      Common areas of occurrence
        Everywhere, but especially common in PHP and ASP apps due to the prevalence of "older" programming interfaces that are still in use.
          Ex: Wordpress has a high level of vulnerability

      Ex Flow:

      Attacker -> Website -> Database
        Give overarching explanation first and then go into specifics below
        Add in picture from: https://www.google.com/search?q=sql+injection&espv=2&biw=1107&bih=757&source=lnms&tbm=isch&sa=X&ved=0ahUKEwiR-4S9rrPKAhUJLyYKHRTQBcEQ_AUICSgE

        Assumptions (Vulnerable Code):
          Will see why this is vulnerable towards the end

          URL Structure: www.snuggie4cats.com/TABLE?id=NUMBER
          SQL Statement: SELECT * FROM TABLE WHERE ID = NUMBER
            (Color Code this)
            In plain english: Select every entry in TABLE where the condition (ID = NUMBER) is true 

          More specifically:
          URL Structure: www.snuggie4cats.com/products?id=1
          SQL Statement: SELECT * FROM PRODUCTS WHERE ID = 1
            (Color code this)

            In plain english: Select every entry in the PRODUCTS table where the condition (ID = 1) is true


      1) Attacker forms an malicious HTTP request to the web server
          The malicious part of the request might be inside the query string, etc.

          Ex: Attack 
          Normal: www.snuggie4cats.com/products?id=1

          Exploit: 
            First, attacker will see if they can access a USER table
              www.snuggie4cats.com/users?id=1

            If successful, there are multiple ways that the attacker can exfiltrate the data.  I'll utilize a more complicated one for learning purposes.

              www.snuggie4cats.com/users?id=1 OR 'a'='a'
              
              Which can translate into the SQL string...
                SELECT * FROM USERS WHERE ID = 1 OR 'a'='a'
                  
                  The addition of the "OR 'a'='a'" makes the WHERE clause always evaluate to true, so if this input isn't properly sanitized, this will essentially run:
                    SELECT * FROM users;
                      Visualize with https://rickbergfalk.github.io/sqlpad/

            How could we mitigate against this?
              We'll go over this later, but it is good to start questioning right off the bat.
              Write down your answers and then compare it my answers at the end.
                This is the only way to really make it stick.

      FOR MAKING TUTORIAL, CURRENTLY AT

      2) Once inside the server, the application logic takes the query string, and creates a SQL statement.  This statement is then run on the database

        In this example, it runs: 
          SELECT * FROM users;


      3) If this operation was successful, the query results are passed back to the web server (which then passes the information back to the user.)

      Attack prevention
        These are some of the most common, remember this is NOT an exhaustive list

        1) Realize trusted/untrusted data
          Ex from above:

          http://www.snuggie4cats.com/TABLE?Id=NUMBER
            
            If a SQL statement that's being generated is:
              SELECT * FROM TABLE WHERE ID = NUMBER

              Untrusted: Anything after the .com
                The route and the query string are being utilized in the SQL statement.
                  Remember this, sometimes the focus is only on the query string.

              Trusted: http://www.snuggie4cats.com
        
        2) Whitelisting (permissions/untrusted inputs)
          Expecting Ex: http://www.snuggie4cats.com/products?Id=1

          Whitelisting permissions
            In general, assign roles to all users on your system.
              Ex: Admin Role, Regular User Role (REG_USER), etc.

            Questions:
              What tables should REG_USER have access to? Whitelist off of those tables.
                Insecure Direct Object Reference Ex: http://www.snuggie4cats.com/users?Id=1

          Whitelisting untrusted input
            Ex: http://www.snuggie4cats.com/products?Id=1

              Be the most granular while taking into account future business considerations.

                Ex: The Id should be a integer between PRODUCT_ID_MIN and PRODUCT_ID_MAX.
                  We create these CONSTANTS in our code.


        3) Parameters
          Is about separating the sql query from the untrusted data.

          ADD WHITELISTING TO NON-Vulnerable CODE


          Two examples in Java: One is vulnerable through SQL injection, the second uses Prepared Statements to mitigate the SQL inject risk.

          Ex: We will learn about parameters through a Java vulnerable code example.  Some code has been left out for simplicity.

          // Connect to db
          Connection dbconnection = DriverManager.getConnection(dbUrl,username, password);

          // Grab the param
          int id = Integer.parseInt(request.getParameter("id"));

          // Vulnerable
          String query = "SELECT * FROM PRODUCTS WHERE ID = " + id;

          // create our query 'holder'
          Statement selectStatement = dbconnection.createStatement();

          selectStatement.executeQuery(query);

          Ex: Non-vulnerable code

          // Connect to db
          Connection dbconnection = DriverManager.getConnection(dbUrl,username, password);

          // Grab the param
          int id = Integer.parseInt(request.getParameter("id"));

          // Vulnerable
          String query = "SELECT * FROM PRODUCTS WHERE ID = " + id;
          
          // Non Vulnerable
          String query = "SELECT * FROM PRODUCTS WHERE ID = ?"

          // create our query 'holder'
          PreparedStatement ps = dbconnection.prepareStatement(query);

          // Set our bind variable
          ps.setInt(1, id);

          ps.executeQuery();

          // Go into how Prepared statements work from Wikipediasure







      What can an attacker to with SQL
        1) Running commands on the underlying host machine

          Certain SQL statements (on certain databases) can access the server's CLI.

          Ex: xp_cmdshell on Microsoft's SQL Server 2014

            EXEC xp_cmdshell 'dir *.exe';
            GO

          This will return a listing of all files
            Obviously, it can get much worse than this.

          The commands that are issued have the same privilege level as the server

          This is turned off by default, but it wasn't always that way.  Thus there are vulnerable systems out there.

        2) Reading sensitive data from the database (Ex: SSNs)
        3) Modifying data in the database (Ex: account balances)
        4) Executing administrative operations on the database (shutting it down)


    Ex: Code review
      The route the developer is expecting
          www.snuggie4cats.com/search?id=4,category=products
      


      What problems do you see with this?
        Probs: Concatenation, no whitelisting of params.

        Additional questions: A attacker could modify the request to access different tables.  Do we have sensitive tables that should be locked down from a non-admin?  Most application servers simply connect to the DB with an admin account (thus have full privileges).
          An example of Insecure Direct Object Reference, which we will touch upon in a different course.
        
      // Make sure that I have colons everywhere in code
