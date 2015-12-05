Rollback
  Reverts write operations on a former primary when the member rejoins the RS after a failover.
    The Rollback only occurs if the primary accepted write operations that the secondaries had NOT replicated before the primary stepped down.
      A Rollback can also occur if the information is replicated to the secondary, but the secondary doesn't stay available to the majority of the RS members.
        (Maybe network partition, etc.)

    When the primary rejoins as a secondary, it "rolls back" its write operations so it's consistent with the state of the other RS memebers.

    Rolled Back Data
      dbPath/rollback

When trouble shooting RS, look at the optimeDate of the primary and secondaries.  Make sure that the secondaries match the optimeDate of the primary

bsondump
  Converts BSON files into human-readable formats (JSON, etc.)

  Can be utilized to read the files from mongodump

  Is a dianostic tool for inspecting BSON files.  It's not used for data ingestion or for any other functionality.

  Ex:
    bsondump collection.bson > collection.json
