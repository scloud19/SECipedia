Tutorial: T

Code Level Security
  Big Areas

  Permissions
    Looks at the privileges that a piece of code has.
      Ex: Read/write access to filesystem? If so, what files?
          Access to network resources? If so, code in what directories can initiate these connections?
            Ex: Linux, code needs privileged access to access ports 1024 (and lower)

          Takeaway: These situations can get very nuanced and we need a high level of granularity :)

  Policies
    Instructs which permissions a given portion of code can utilize

    These policies can be placed in a policy file

  Enforcement
    If a policy is violated, what action do we trigger?

    Obviously an alert should be generated and we should deny any action
      In the JVM, a Security Exception is thrown.


Java History: Security Model
  Initial model (before Java 2)
    Run all code inside a sandbox and restrict that sandbox to certain resources
      Remember our need for a high level of granularity.
        The sandbox model didn't allow for this flexibility in access control.

  Java 2+
    Gave us more granularity in access controls
      Relative to the location that the code is being loaded from, we can now set permissions.


  T: Trans to permissions.md