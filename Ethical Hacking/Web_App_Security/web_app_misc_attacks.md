
Changing the price of a product that is transmitted in a hidden HTML form field to fraudulently purchase the product for a cheap amount.

If you compromise a user's email account, you have access to everything.

Access Control Flaws
  Often the developer makes wrong assumptions about how a user will interact with the application and will omit access control checks from some application functions.

  To find these, it is tedious.  You need to do probing on every independent part of the application.

Tokenizing Flaws
  Usually created when trying to blacklist

  exs:
    SELECT/*foo*/username,password/*foo*/FROM/*foo*/users
      Utilizing comment syntax


    <img%09onerror=alert(1) src=a>
      Using encoded characters


  Black list filters in WAFs
    Vulnerable to NULL byte attacks
      Inserting a NULL byte anywhere before a blocked expression can cause some filters to stop processing the input and therefore not identify the expression.
        Ex: %00<script>alert('muhah')</script>

      These types of attacks are relatively common in the web app security world.
        As the NULL byte can act as a string delimiter (in certain contexts), it can be used to terminate a filename or a query to some back-end component.

    In some browsers, NULL bytes are simply ignored; so arbitrary null bytes can be inserted with blocked expressions to defeat certain blacklist-based filters.
