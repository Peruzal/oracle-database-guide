# Starting and Stopping the Database

## Startup and Shutdown an Oracle Database

Use SQL*Plus to start and instance and open a database.

1. Logon as the oracle user
2. Setup the environment variables
```sh
oraenv
```
3. Connect as the SYS user with operating system authentication.
```
sqlplus / as sysdba
```
4. Start the instance only.
```sql
startup nomount;
```
5. Mount the database
```sh
alter database mount;
```
6. Open the database
```sh
alter database open;
```
7. Confirm the database is open
```sh
select opn_mode from v$database;
```
The database should be in READ WRITE state.
8. Shutdown the database
```sh
shutdown immediate
```
9. Start the database with one command
```sh
startup
```
The database should start and be in the OPEN state.

## Automatically start the database on boot

To automatically shutdown and start the database you will need to use the `dbstart` and `dbshut` scripts located in the `$ORACLE_HOME/bin` folders

1. Login in as the root user.
2. Change the file `/etc/oratab`. The file lists databases that should be automatically started in the following format :

```sh
$ORACLE_SID:$ORACLE_HOME:<N|Y>
```

For example for an Oracle 12 c installed in `u01/app/oracle/product/12.2.0.1/db_1/` the entry would be as follows :

```sh
ORCL:/u01/app/oracle/product/12.2.0.1/db_1//:Y
```

`ORCL` is the database name and the `Y` at the end is to auto start the database on boot.


### Auto start with Sys V Scripts

The older way of startin the instance is to use the SysV init scripts located in the `init.d` folder.

Create a file `dbora`

```sh
vi /etc/init.d/dbora
```

Add the following into the script and update the `ORACLE_HOME` to the appropriate folder :

```sh
#! /bin/sh -x
#
# chkconfig: 2345 80 05
# description: start and stop Oracle Database Enterprise Edition on Oracle Linux 5 and 6
#

# In /etc/oratab, change the autostart field from N to Y for any
# databases that you want autostarted.
#
# Create this file as /etc/init.d/dbora and execute:
#  chmod 750 /etc/init.d/dbora
#  chkconfig --add dbora
#  chkconfig dbora on

# Note: Change the value of ORACLE_HOME to specify the correct Oracle home
# directory for your installation.

ORACLE_HOME=/u01/app/oracle/product/12.2.0.1/db_1

#
# Note: Change the value of ORACLE to the login name of the oracle owner
ORACLE=oracle

PATH=${PATH}:$ORACLE_HOME/bin
HOST=`hostname`
PLATFORM=`uname`
export ORACLE_HOME PATH

case $1 in
'start')
        echo -n $"Starting Oracle: "
        su $ORACLE -c "$ORACLE_HOME/bin/dbstart $ORACLE_HOME" &
        ;;
'stop')
        echo -n $"Shutting down Oracle: "
        su $ORACLE -c "$ORACLE_HOME/bin/dbshut $ORACLE_HOME" &
        ;;
'restart')
        echo -n $"Shutting down Oracle: "
        su $ORACLE -c "$ORACLE_HOME/bin/dbshut $ORACLE_HOME" &
        sleep 5
        echo -n $"Starting Oracle: "
        su $ORACLE -c "$ORACLE_HOME/bin/dbstart $ORACLE_HOME" &
        ;;
*)
        echo "usage: $0 {start|stop|restart}"
        exit
        ;;
esac

exit

```

Change the permissions on the `dbora`

```sh
chmod 750 /etc/init.d/dbora
```

Add the service

```sh
chkconfig --add dbora
```

Change the service to auto start on boot

```sh
chkconfig dbora on
```

Start the service to test

```sh
service dbora start
```

Check if the service is running

```sh
service dbora status
```

Reboot the machine and verify the service automatically starts


## Automatically start service using Systemd

The system d scripts are located in the `/etc/systemd`. They are separated into user and system scripts.

Create a file `oracle-dbms`

```sh
vi /etc/systemd/system/oracle-dbms
```

Add the following into the file

```sh
[Unit]
Description=Oracle Database(s) and Listener
After=syslog.target network.target
Requires=network.target
[Service]
#Type=forking
Type=idle
RemainAfterExit=yes
Restart=no
ExecStart=/u01/app/oracle/product/12.2.0/dbhome_1/bin/dbstart /u01/app/oracle/product/12.2.0/dbhome_1
ExecStop=/u01/app/oracle/product/12.2.0/dbhome_1/bin/dbshut /u01/app/oracle/product/12.2.0/dbhome_1
User=oracle
[Install]
WantedBy=multi-user.target
```

Reload the service

```sh
systemctl daemon-reload
```

Enable the service

```sh
systemctl enable oracle-rdbms
```

Start the service

```sh
systemctl start oracle-rdbms
```

Check the service 

```sh
systemctl status oracle-rdbms
```

Reboot the system and check if oracle automatically startssu root
