Aurora
  AWS's proprietary DB that's mySQL compatible.  It is meant to be a drop-in replacement for MYSql.
    
  Up to 5x better performance than mySQL at a price point one tenth
  that of a commercial database

  Scaling
    Start with 10Gb and scales in 10Gb increments to 64Tb
    Compute resources can scale up to 32vCPUs and 244Gb of Memory

  Availability
    2 copies of your data is contained in each AZ, with a minimum
    of 3 AZ (6 copies of data)
      This is great from a redundancy perspective but you can
      also get reads from these nodes as well

      Z: Is the minimum AZ still 3? It might now be 2.

    Aurora is designed to transparently handle the loss of up to 2
    copies of data without affecting database write availability and
    up to three copies without affecting read availability.

  Aurora storage is also self-healing. Data blocks and disks are continuously scanned for errors and repaired automatically.

  Administration
    AWS RDS provides these services
      provisioning, patching, backup, recovery, failure detection, and repair.

    Migration tools
      Are provided to convert your existing AWS RDS MySQL to Aurora.
