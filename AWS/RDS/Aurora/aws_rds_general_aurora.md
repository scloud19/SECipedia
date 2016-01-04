Aurora
  Very important implications for failover
    Amazon Aurora Replicas can act as a failover target with NO data loss, where MySQL replicas can act as a failover target, but there's potentially minutes of data loss.

    Check out this for other important implications on why Aurora is the best
      https://aws.amazon.com/rds/aurora/faqs/

  AWS's proprietary DB that's mySQL compatible.  It is meant to be a drop-in replacement for MYSql.

  Up to 5x better performance than mySQL at a price point one tenth
  that of a commercial database
    Amazon Aurora delivers significant increases over MySQL performance by tightly integrating the database engine with an SSD-based virtualized storage layer purpose-built for database workloads, reducing writes to the storage system, minimizing lock contention and eliminating delays created by database process threads. Our tests with SysBench on r3.8xlarge instances show that Amazon Aurora delivers over 500,000 SELECTs/sec and 100,000 updates/sec, five times higher than MySQL running the same benchmark on the same hardware. Detailed instructions on this benchmark and how to replicate it yourself are provided in the Amazon Aurora Performance Benchmarking Guide.

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

    
