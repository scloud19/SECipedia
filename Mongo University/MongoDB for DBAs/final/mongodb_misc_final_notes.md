Rollback
  Reverts write operations on a former primary when the member rejoins the RS after a failover.
    The Rollback only occurs if the primary accepted write operations that the secondaries had NOT replicated before the primary stepped down.
      A Rollback can also occur if the information is replicated to the secondary, but the secondary doesn't stay available to the majority of the RS members.
        (Maybe network partition, etc.)

    When the primary rejoins as a secondary, it "rolls back" its write operations so it's consistent with the state of the other RS memebers.

    Rolled Back Data
      dbPath/rollback
