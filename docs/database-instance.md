# Managing the Oracle Database

An Oracle database system consists of an **Oracle database** and an **Oracle instance** (in an Oracle Real Application Clusters environment, there can be more than one instance).

A **database** consists of a set of disk files that store user data and metadata. **Metadata**, or "data about the data," consists of structural, configuration, and control information about the database.

An Oracle **instance** (also known as a **database instance**) contains the set of Oracle Database background processes that operate on the stored data and the shared allocated memory that those processes use to do their work.

Each instance has an instance ID, also known as a **system ID (SID)**.

For a local connection, you identify the instance by setting operating system environment variables **ORACLE_SID** and **ORACLE_HOME**.

The properties of an Oracle instance are specified using **instance initialization parameters**.When the instance is started, an initialization parameter file is read, and the instance is configured accordingly.

## Single Instance Database Architecture

The processes that make up the instance are called background processes, because they are present and running at all times while the instance is inactive.

Server processes execute SQL on behalf of the user. They are referred to as foreground processes. Associated with each server process is an area of memory called the PGA, which is nonshareable.


## Identify Server Components


Determine if the instance is part of a RAC database

```sh
select parallel from v$instance;
```

This will return NO for singel instance database;

Determine if the database is protected against data loss by a Data Guard standby database

```sh
select protection_lvel from v$database;
```

Determin whether Stream has been configured for the database

```sh
select * from dba_streams_administrator;
```


Identify the physical structures of the database

```sh
select name, bytes from v$datafile;
select name, bytes from v$tempfile;
select * from v$controlfile;
select member from v$logile;
```

Identify memory processes

```sh
$> ipcs -m
ps -ef | grep ora_
```

## Memory Structures

The SGA will contain three data structures at a minimum :

- Database buffer cache
- The log buffer
- The share pool

### Database Buffer Cache

The database buffer cache is work area for executing SQL.When updating data, user's sessions don't directly do so on disk. The data blocks containing the data are first copied into the database buffer cache. 

Ideally. all the blocks containing data that is frequently accessed will be in the database buffer cance, minimizing the need for disk I/O.

A dirty buffer contains block that are not the same as the ones on disk. Dirty buffers are eventually written to disk.

The size of the database buffer cache is critical for performance. The cache should be sized adequately for caching all the frequently accessed blocks.


### The Log Buffer

The log buffer is a small, short term staging area for change vectors before they are written to the redo log on disk. A change vector is a modification appled to something executing, executing DML statements generates change vectors applied to data. The redo log is the database guarantee that data will never be lost. Whenever a data block is changed, the change vectors applied to the block are written out to the redo log, from where they be extracted and applied to datafile backups if it ever necessary to restore a datafile.

The **LGWR** is a background process that writes the redo log buffers to the redo log files on disk. This happens in near real time when a COMMIT statement is issued;

The size of the log buffer is fixed at instance startup and it can not be changed automatically.

```sh
show parameter log_buffer;
```

The process of slushing the log buffer to disk is one fo the ultimate bottlenecks in Oracle architecture. You cannot do DML faster than the LGWR can flush the change vectors to the online redo log files.

!!! note
    If redo generation is the limiting factor in a database's performance, the only option is to to RAC. In a RAC database, each instance has its own log buffer and its own LGWR.

### The Shared Pool

Is the most complex of SGA structures. It is divided into hundreds of substructures, all of which are managed internally by Oracle server.

- The libary canche
- The data dictionary cache 
- The PL/SQL cache
- The SQL query and PL?SQL function result cache

### Shared Pool

```sh
show parameter shared_pool_size
```

The shared pool size is dynamic and can managed automatically.

### Library cache

The library cache is a memory area for storing recently executed code, in its parse form. 

### The Data Dictionary Cache

The data dictionary cache is sometimes referred to as the row cache. It stores recently used object definitions: descriptions of tables, indexes, users and other metadata definitions.

For performance, the algorithms Oracle uses to allocate memory in the SGA are designed to favor the dictionary cache, so if the library cache is correct, the dictionary cache will already be correct.


## Investigate Memory Structures

Connect to the system as the user SYSTEM

Use the `show SGA` command

```sh
SQL> show sga
```

Show the current, maximum and minimum sizes of the SGA components that can be dynamically resized

```sh
select component, current_size, min_size, max_size from v$sga_dynamic_components;
```

Determine how much memory has been, and is currently allocated to Program Global Areas:

```sh
select name, value from v$pgastat where name in ('maximum PGA alocated', 'total PGA allocated');
```


Manual check point

```sh
alter system checkpoint;
```

## Investigate the Processes Running

Connect to the system

Determine what processes are running and how many each

```sh
select program, paddr from v$session order by program;
select program, addr from v$process order by program;
```

Each process must have a session. 

Check how many processes are running

```sh
select name, description, paddr from v$process order by paddr;
```

On terminal count the number of processes

```sh
ps -ef | grep LOCAL | wc -l
```


## Storage Structures

The smallest storage unit is a block. Usually the operating system block size is matched with the Oracle block size. The oracle should not be bigger than the operating sytem block.

![Storage Structures][2]


## Investigate Storage Structures

Connect to the database as the user SYSTEM

Create a table without nominating a tablespace - it will be created in your default tablespace, with one extent:

```sql
create table test(c1 varchar2(10))
segment creating immediate;
```

Identify the tablespace in which the table resides, the size of the extend, the file number the extenets is in, and at which block of the file the extent starts :

```sql
select tablespace_name, extent_id, bytes, file_id, block_id from dba_extents where owner='SYSTEM' and segment_name='test'
```

Identify the file name(substitute the file_id from the previous query when prompted) :

```sql
select name from v$datafile where file#=&file_id;
```

Work out exactly where in the file the extent is, in terms of how many bytes into the file it begins. This requires finding out the tablespace's block size. Enter the block_id and tablespace_name returned from the previous query :

```sql
select block_size * &block_id from dba_tablespaces 
where tablespace_name='&tablespace_name';
```


## Initialization Parameters

The Oracle instance reads initialization parameters from a file at startup. The parameters are retained in memory and can be changed dynamically.

The type of file used to start the instance determines if dynamic initialization parameter changes persist across database shutdown and startup. The parameter file types are:

1. Server parameter file
2. Text initialization parameter file

## Server parameter file

The server parameter file, commonly known as the **SPFILE**, is the preferred form of initialization parameter file, and is a binary file that can be written to and read by the database. It must not be edited manually.

!!! note
    When changing an initialization parameter in the server parameter file, you can also specify that the in-memory value be changed, so that your change is reflected immediately in the current instance. If you do not change the in-memory value, then the change does not take effect until you shut down and restart the database.

## Text initialization parameter file

A text initialization parameter file is a text file that can be read by the Oracle instance, but it is not written to by the instance. You can change a text initialization parameter file with a text editor, but changes do not take effect until you restart the Oracle instance. 


## Create server parameter file from text initialization file


## Create text file from server file


## Server file from in-memory parameters

## Background Processes

The background processes of the Oracle instance manage memory structures, asynchronously perform I/O to write data to a file on a disk, and perform general maintenance tasks.

### Database writer (DBWn)

The database writer writes modified blocks from the database buffer cache to the files on a disk. Oracle Database allows a maximum of 36 database writer processes.

### Log writer (LGWR)

The log writer process writes redo log entries to disk. Redo log entries are generated in the redo log buffer of the System Global Area (SGA) and the log writer process writes the redo log entries sequentially into an online redo log file.

### Checkpoint (CKPT)

At specific times, all modified database buffers in the SGA are written to the data files by a database writer process (DBWn). This event is called a **checkpoint**. The checkpoint process signals DBWn, updates the data files and control files of the database, and records the time of this update.

### System monitor (SMON)

The system monitor performs instance recovery when a failed instance is restarted.

### Process monitor (PMON)

The process monitor performs a recovery when a user process fails. It cleans up the cache and frees resources that the failed process was using.

### Archiver (ARCn)

Archiver processes copy the online redo log files to archival storage when the log files are full or a log switch occurs. The database must be in archive log mode to run archive processes.

### Manageability monitor (MMON)

This process performs various management-related background tasks, for example:

- Issuing alerts whenever a given metric violates its threshold value
- Taking snapshots by spawning additional processes
- Capturing statistical values for SQL objects that have been recently modified

### Job Queue Processes (CJQ0 and Jnnn)

Job queue processes run user jobs, often in batch mode. A job is a user-defined task scheduled to run one or more times.


## Memory Structures

The sizes of the instance memory structures affect database performance and are controlled by initialization parameters.

Upon installation, you can let the database manage memory for you automatically, or you can choose to manually configure the instance memory structures.

The most important memory structures are the :

- System Global Area
- Program Global Area


## System Global Area

![Oracle Instance][1]

The System Global Area (SGA) is a group of shared memory structures, known as SGA components, that contain data and control information for one Oracle Database instance. The SGA is shared by all server and background processes.

Components of the SGA :

1. Database buffer cache
2. Shared pool
3. Redo log buffer
4. Large pool
5. Java pool
6. Streams pool
7. Result cache

## Program Global Area

A Program Global Area (PGA) is a memory region that contains data and control information for a server process. It is nonshared memory created by Oracle Database when a server process is started. There is one PGA for each server process.

The total PGA memory allocated for all background and server processes attached to an Oracle Database instance is referred to as the **total instance PGA memory**, and the collection of all individual PGAs is referred to as the **total instance PGA**, or just **instance PGA**. The PGA is used to process SQL statements and to hold logon and other session information. 





[1]: images/instance.png
[2]: images/storage-model.png



