# Performing Database Backup

Oracle uses RMAN(Recovery Manager) command line tool to perform database operations.

## Perform a full backup

Setup the environment variables

Connect to RMAN

```sh
rman target /
```

To perform a consistent backup, the database should bot run be open. Close the database and start it in mount mode.

```sh
shutdown immediate;
```

Re-open the database in mount mode

```sh
startup mount;
```

Backup the database

```sh
backup database;
```

Reopen the database. The database will be backed up to the fast recovery area.

```sh
alter database open;
```

## Backup database without shutting it down

The database can be backup up whilst its open if its in archive log mode.

To backup a the database in archive mode run :

```sh
backup database plus archivelog
```

The above command relies with defaults already setup in place.

### Backup the tablespace

```sh
backup tablespace sysaux;
```

### Backup archive log file 

```sh
backup archivelog all;
```

## Create Incremental Backups

Incremental backups starts with the full backup and then add the changes between the last backup. The initial backup is called level 0 and the next is level 1. Incremental backups should always be performed as backupsets.

```sh
backup as backupset incremental level 0 database;
```

```sh
backup as backupset incremental level 1 database;
```

## Reports on backups

RMAN keeps a repository in the database control files. The repository is stored in the database's controlfile.

RMAN has two commands that will tell you the state of your backups:

- LIST - tell you what backups there
- REPORT - will tell you what backups are needed

List all backups that have been made and are still recorded in the repository

```sh
list backup of database;
```

```sh
list backup of tablespace system;
```

List backups of archived redo logs;

```sh
list backup of archivelog all;
```

List everything that needs to be backed up according to RMAN's configured retention policy. 

```sh
report need backup;
```

List all backups that are no longer needed according to RMAN retention policy.

```sh
report obsolete;
```

## Administering Backups

Server-managed backups are largely automatic but they are circumstances where intervention is needed, e.g when backup file is moved to a different location. RMAN will need to be alerted.

The commands available are :

- CROSSCHECK - A crosscheck forces RMAN to compare its repository with the real world. If will check that all the backups it has made do still exist, on disk and tape. Missing files are flagged as EXPIRED.

- DELETE EXPIRED - Thei will remove all references from the repository to expired backups. If the backups have been moved, an alternate is to mark them as UNAVAILABLE.
- DELETE OBSOLETE - The will force RMAN to apply its retention policy. It will delete all backups that it considers to be no longer necessary from disk ot tap.
- CATALOG - The command let you inform RMAN about relocated backups, so that RMAN can include them in its repository in their current location.

## Using Database Recovery Advisor(DRA)

The Database Recovery Advisor is a facility for diagnosing and repairing problems with a database. There are two interfaces: The RMAN executable and Enterprise Manager.

The ADR repository is located in the parameter specified by the `diagnostic_dest` parameter. The Health Monitor performs a set of checks that run automatically when certain error conditions arise, or manually in response to the DBA's instructions.

The DRA can perform several types of checks but only if the database is `nomount` abd higher.

## Using the DRA

The instance should be started. If the paramter file is broken, RMAN can start the instance without a parameter file only using the `ORACLE_SID` environment and the `DB_NAME` parameter. This ability may mean that is possible to bootstrao a restore and recovery operation from nothing.

The process is as follows :

- Access data failures 
- List failures
- Advise on repair
- Execute repair

### List failures

Identify problems. Problems will be show with their severity level.

```sh
list failure;
```

### Get recommendations

```sh
RMAN> advise failure;
```

### Fix the problem

To execure the repair script automatically execure the following. 
```sh
repair failure;
```

If the damage is to a controlfile copy, the instance will stop in nomount mode.

## Use the DRA to Dianose and Repair a Problem

1. Connect to RMAN
2. Confirm that there is abackup of they SYSAUX with RMAN.
```sh
list backup of tablespace sysaux;
```
3. If this does not return at least one backupset of type FULL, make a make a backup with the following:
```sh
backup database plus archivelog;
```
4. Shutdown the instance and exit from RMAN:
```sh
shutdown immediate;
exit;
```
5. Delete the datafiles for the SYSAUX tablespace.
6. Connect with SQL*Plus and attempt to startup This iwll stop in mount mode, with an error regarding the missing file.
7. Launch RMAN
8. Diagnose the problem
```sh
list failure;
```
9. Get advise on the failure
```sh
advise failure;
```
This will suggest that you should restore and recover the datafile and generate a repair script. Open the script and check its contents
10. Repair the failure
```sh
advise failure;
```