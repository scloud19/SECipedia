Ex: 11.30.0.52
     1  2 3 4
  1
    Parent value
      Represents major evolutionary steps for cPanel & WHM
  2
    Major value
      A new set of features
      Most often, this is the most useful value in the product version number

      Odd Values
        Development releases (UNSTABLE)
          Available ONLY in these tiers
            EDGE
            CURRENT
      Even Values
        Production releases (STABLE)
          Usually 3 per year
          Available by these tiers
            EDGE
            CURRENT
            RELEASE
            STABLE
              This is the last tier of the release cycle.
                Have your application upgrade off of this tier
  3
    Minor value
      Represents a set of bug fixes and changes that stabilize the features of the MAJOR VALUE
  4
    Build Value
      A unique build of cPanel & WHM
        No upper limit on this value  

  CURRENTLY_AT
    For more information about the /etc/cpupdate.conf file, read our The cPanel & WHM Update Configuration File documentation.


As of cPanel & WHM version 54, the UI no longer includes the parent value in the version number.
  However, config files and API functions will contrinue to use the parent version num.