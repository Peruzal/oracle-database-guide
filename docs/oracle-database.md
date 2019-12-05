# Oracle Database

The location of the init file is in the $ORACLE_HOME/dbs directory.

## Tablespaces 

- `SYSTEM` - Contains the database dictionary
- `SYSAUX` - Objects that are not associated with the data dictionary but are very closely related.
- `USERS` - Used for storing the users default permanent objects eg tables.
- `TEMP` - The temporary tablespace for temporary sessions used by sessions.
- `UNDOTBS1` - Used for undo segments which are necessary to ensure transactional consistency.

## Starting the database with the initialize file

startup nomount pfile=<location of the pfile>


## Show installed components

The installed components are store in the dba_registry

```sh
select comp_name, version, status from dba_registry order by 1;
```


Dynamic performance views

```sh
select instance, status, logins, instance_mode from v$instance
```

```sh
select name, open_mode, log_mode from v$database;
```

```sh
select name from v$datafile;
```

```sh
select * from v$controlfile
```

```sh
select member from  v$logfile
```


## Dropping the database

```sh
shutdown immediate
startup mount restrict;
drop database;
```


![Oracle Database Architectures][1]

[1]: images/oracle-database-architecture.png