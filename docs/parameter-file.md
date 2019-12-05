# Oracle Parameters

Here are the most parameters for the database:

## Show the name of the database

```sh
show parameter db_name
```

The parameter names the database to which the instance will connect. The name is embedded in the control files.

## Control files location

```sh
show parameter control_files
```

Shows the location of the control files. The control files have pointers to the other files, the database data files, the online redo logs and the mapping of the datafiles to tablespaces.