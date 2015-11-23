Currently at: Protecting Against Cross Site Request Forgery
Before proceeding, need to watch Hack yourself first and hack-proofing your ASP.NET applications


To do: look at attack.com and how this XSS attack is implemented

Always assume that the client and network (leading to the server) is compromised

One of the biggest problems is that the API's that communicate with the front end are usually set up to blindly ac


    Securing admin area templates or other information
        Exposing these types of templates to the public could potentially show the underlying workings of your system.  In general, err on the side of caution.  On the server, only allow these types of templates to be given to an authenticated user.

    When launching web apps into production, always minify your code.  While this can be reverse engineered, it makes exploits more difficult to spot.  Also, remove all comments.

    Client side code often reveals API's with insufficient access controls
        API's often expose too many attributes

    Angular naturally encodes/sanitizes HTML input so that XSS attacks are less prevelent 

    Protecting against Cross Site Request Forgery

