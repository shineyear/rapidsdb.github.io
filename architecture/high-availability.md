---
layout: default
title: High Availability
parent: Architecture
nav_order: 10
---

# High Availability

---

High reliability (HA) is the ability of a system to operate continuously without failing for a designated period of time. It ensures that a system consistently performs according to its specifications, meeting the uptime expectation. Reliability can be expressed by the term “100% operability” or “never failed”. In the information technology industry, a widely held but difficult- to-achieve standard of availability is known as the famous five-nines standard, that is, the system is available 99.999% of the time.

However, in reality, it is impossible for a computer system to be available 100% of the time. It can inevitably encounter hardware malfunctions, software failures, operator errors or malicious attacks during operation. All of these problems may interrupt ongoing transactions and cause data loss, corruption or storage failures in some serious cases. Therefore, a database management system should have a reliable data protection mechanism built in to ensure that the database can restore to the correct state after disruptions and the critical system can remain operational.

RapidsDB provides complete backup and recovery solutions. Although it leverages main memory to store data, copies of data are maintained on the server disk. When RapidsDB runs in a fully persistent mode, transactions are committed to transaction logs on disk and compressed into database snapshots. The directory to the snapshot and log files can be modified through the configuration file. Once the database restarts, it will load the snapshots and logs and restore itself to the state before being shut down.

Both snapshot and log files are in the internal binary format. Because RapidsDB will re-index when it restores or copies data, indexes will not be stored in these files. Instead, only row data will be saved. As a result, the size of snapshots is usually smaller than that of the used memory.

A verification process is established to check both snapshot and log files to ensure the integrity of data. When an inconsistency is detected, the database will temporarily be in the offline state, in which any connection to the database is disabled. The damaged part of a log will be automatically truncated and repaired. The database will load as much data as possible from the log into the memory and then change the database state back to be online. As RapidsDB cannot guarantee the consistency of the data at that time, any damaged data in the log file cannot be recovered.

If the operation of a damaged log is the same as that of future logs, it will generate wrong results when the damaged log is replayed. If RapidsDB finds an inconsistency during the verification process when snapshots are replayed, the database will enter the unrecoverable state.  In that case, RESTORE command must be used to restore the database from a backup.

For data stored in memory, RapidsDB will asynchronously load data from snapshots and logs into memory when it starts. This is an automatic process.  The recovery status of the database can be viewed by running the SHOW DATABASES command.  For data stored in the columnar store on disk, data loss will not occur when the database restarts.  To be safe, a single table or the entire database can be manually backed up and restored in a specified directory.  The manual backup operation is applicable to both row-based and column-based tables.
