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
          https://httpd.apache.org/docs/2.4/programs/htpasswd.html

        -B
          Use bcrypt encryption for passwords.

          Other options provided aren't secure: MD5, SHA, crypt
            Apache uses Md5 by default, which even with salting, isn't a very secure solution.




        Manages the user account file
          Which can be placed /etc/httpd/conf
          DO NOT PLACE IN DOCUMENTROOT!

        C