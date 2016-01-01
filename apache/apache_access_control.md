Also see apache_authentication_author.md

In the end, I figured that apache authentication is way too convoluted vs other options (oauth, openID, etc.) I'd recommend implementing these services instead of trying to do a web authentication scheme from apache.  I did my notes up to STOPPED_HERE


Access Control
  In a typical scenario, we can seperate this into 3 sections in our website

    Public Section

    Members Section
      Unique information for a member
        Ex: Order history, etc.

    Admin section

    Setup: Where to keep the user account info?
      Dont utilize MD5

      Text files (that are encrypted)
        Me: Security implications?

        Fine to 1 to a few hundred users
      DBM files
        Support indexing by a hash
          Faster lookup

        Support faster keyword based lookup in the file

        Good for a few thousand to a few million users

      Database
        I'd recommend for most PROD ENVs as it's extensible and you probably already are using one


      Tutorial: For the sake of our lecture, lets store our passwords in an encrypted text file using htpasswd

      htpasswd -B
        Security implications?
          Make sure to read and then cover
          https://httpd.apache.org/docs/2.4/programs/htpasswd.html

        -B
          Use bcrypt encryption for passwords.

        -c
          Create the file

          Other options provided aren't secure: MD5, SHA, crypt
            Apache uses Md5 by default, which even with salting, isn't a very secure solution.
              In future lecture, talk about insecure hashing algs/salting.  Look at http://blog.codinghorror.com/speed-hashing/

        Tutorial: Lets create two users
          htpasswd -B -c /etc/httpd/conf/uf USER_NAME    
          htpasswd -B /etc/httpd/conf/uf USER_NAME
            
            Don't make the filename "users" etc.  Very easy for an intruder to do a search for common file names
              Ex: used "uf" for UserFile

          cat /etc/httpd/conf/uf
            Look at the hashes

          What about permissions on the uf file?  

          Configuring the Access Control
            TODO: Go back through each of the directives and look at best practices.  AuthType Basic sends passwords in clear text, so this wouldn't be a good idea.  Look at digest authtype.  Do tutorial with best practices for this area.

            <Directory /var/www/html/daMAN/admin>
              AuthType Basic

              AuthName "Log into the admin area"
              # Define the authorization realm.  This string is displayed in the dialog box when prompting the user for ID/Password.  Think of this as a descriptor for the realm that you're trying to protect.

              AuthUserFile /etc/httpd/conf/uf
              
              Require valid-user
              # You can supply an explicit list of users here
              # valid-user is any authenticated user from the AuthUserFile.  Implications for different levels of privilege.
                # Explicitly list users
                # Are there user groups?

            </Directory>


            How does HTTP Authentication Work?




            









        Manages the user account file
          Which can be placed /etc/httpd/conf
          DO NOT PLACE IN DOCUMENTROOT!

  