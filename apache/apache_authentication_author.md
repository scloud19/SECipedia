T: Give credit in the beginning to the apache docs.  I meticulously went through those docs to come up with this tutorial.  Tried to simplify things.

In t


TODO: Merge this with apache_access_control.md

Authentication
  "Is this person who they say they are?"

Authorization
  "What resources can a given person access?"

When implementing authentication and authorization you usually need to choose at least one module from each module group
  
  Groups
    Authentication type, Authentication provider, Authorization

    T: We will be going over each area in-depth

    1) Authentication type module grouping
        Modules
          mod_auth_basic
          mod_auth_digest
          mod_auth_form

          Like always, there are non-native modules that are available to you.  If you want to utilize Kerberos Authentication, look at http://modauthkerb.sourceforge.net

          Each of these modules implements a different type of authentication

          In order for this authentication to be carried out, 2 directives are required
            AuthType
            Require

            AuthType
              Specifies the type of user authentication for a directory

              Types available
                None

                Basic
                  implemented by the mod_auth_basic module

                  Provides HTTP Basic Authentication
                    I'll be doing a follow up lecture on this and other common authentication schemes.

                    Caveat: Sends passwords from browser to server utilizing Base64 encoding.
                      Anyone can decode this very easily.

                      Thus, you need to implement SSL when utilizing this auth scheme.

                Digest
                  implemented by the mod_auth_digest module

                  Implements HTTP Digest Authentication
                    Utilizes MD5 hashing which has been shown to be insecure.
                      For this (and other reasons), don't use digest.

                Form
                  implemented by the mod_auth_form module

                  Allows for an HTML login form for authentication.

                  Once the user has been authenticated through this mechanism, the users' login details will be stored in a session that's created by the mod_session module.

                  As form based authentication is the most user friendly way to authenticate; I'll be doing a follow up lecture on this topic in Apache.

            Ex:
            <Directory "/var/www/html">
                AuthType Basic
                Require valid-user
                  # For example,this depends on the mod_authz_user module
                  # Look at docs for all directives to make sure 
                  # you're picking the correct combinations of the 
                  # 3 aforementioned groupings

            </Directory>

              Remember that I spoke about how modules from the 3 groupings need to be utilized (for most scenarios).

    2) Authentication provider module grouping
        When logging in an individual, his/her credentials are validated against a provider.  

        A prov
        Modules inside of grouping (aka providers)
          mod_authn_anon
          mod_authn_dbd
          mod_authn_dbm
          mod_authn_file
          mod_authnz_ldap
          mod_authn_socache

        These modules can be implemented by these directives
          AuthBasicProvider
            For a basic authentication scheme

          AuthDigestProvider provider-name [provider-name1]
            For a digest authentication scheme
            * Not recommended to do digest authentication

            You can specify multiple providers, which will be checked for a 
          In-depth
            AuthBasicProvider
              Sets the authentication provider/s for a given location (i.e., url)

    3) Authorization
      mod_authnz_ldap
      mod_authz_dbd
      mod_authz_dbm
      mod_authz_groupfile
      mod_authz_host
      mod_authz_owner
      mod_authz_user


  
