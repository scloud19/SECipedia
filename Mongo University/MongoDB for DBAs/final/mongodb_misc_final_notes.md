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

Atomic Transactions
  Where a series of database operations either all occur, or nothing occurs.

  This guarantee prevents updates to the database occuring only partially.

  MongoDB
    Write operations are atomic on the level of a single document
      Occurs even if the operation modifies multiple embedded docs within the main master document.

      For a single write operation that modifies multiple documents
        The write operation to the single document IS atomic, but the "write" to the GROUP ISN'T.  Thus, other operations may be mixed in.

        If you need to isolate a single write operation (over multiple docs), use $isolated 
          Although, this doesn't provide "all-or-nothing" atomicity.
            Ex: An error during the write operation doesn't roll back all the changes that occured before.
              If you need "all-or-nothing" look at a two-phase commit.
              Look at https://docs.mongodb.org/manual/tutorial/perform-two-phase-commits/ for more info and caveats


