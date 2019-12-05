# Configuring Oracle Network Environment

## Oracle Net Listener Configuration

The default listener configuration file is called **listener.ora**, and it is located in the **network/admin** subdirectory of the Oracle home directory. For example, if your Oracle home directory is** /u01/app/oracle/product/12.2.0/dbhome_1**, then the listener.ora file is created by default in the **/u01/app/oracle/product/12.2.0/dbhome_1/network/admin** directory.

The listener is automatically configured when you create a new Oracle database. It just works without any configuration changes.

 This default listener is named LISTENER, supports no service names on startup, and listens on the following TCP/IP protocol address:

```sh
(ADDRESS=(PROTOCOL=tcp)(HOST=host_name)(PORT=1521))
```

## Service Registration

The oracle database automatically registers with the listener after startip up. Usually it might take a minute or so for the registration to complete.

**PMON** performs the service registration.

The database can also be manually specified in the `listener.ora` file.

## Connect Descriptors

The client uses a connect descriptor to specify the database to which it wants to connect. This connect descriptor contains a protocol and a database service name.

The following example shows a connect descriptor that enables clients to connect to a database with service name `mydb.us.example.com`:

```sh
DESCRIPTION=
   (ADDRESS=(PROTOCOL=tcp)(HOST=my-server) PORT=1521))
   (CONNECT_DATA=
      (SERVICE_NAME=mydb.us.example.com))
```

## Connection Requests

Users initiate a connection request by providing a connect string. A connect string includes a user name and password, and a connect identifier. The connect identifier name is referred to as a **net service name**.

## Naming Methods

A **naming method** is a resolution method used by a client application to resolve a connect identifier to a connect descriptor when attempting to connect to a database service.


Oracle Net provides support for the following naming methods:

1. Easy Naming
2. Local Naming
3. Directory Naming

### Easy Naming

The easy connect naming method enables clients to connect to an Oracle database by using only a TCP/IP connect string consisting of a host name and service name.

The connection should be in the form `user@"host[:port][/service_name][:server][/instance_name]"`

The place holders used in the connect identifier format represent:

- **host** — the name or IP address of the Oracle database host computer.
- **port** (optional) — the TCP port number on which the Oracle Net listener listens for connections.
- **service_name** — a database service name.
- **server** — the type of service handler. Acceptable values are `dedicated`, `shared`, and `pooled`.

### Examples of Easy Naming

```sh
nick@"dbhost.example.com/orcl.example.com"
nick@"192.0.2.1/orcl.example.com"
nick@"[2001:0DB8:0:0::200C:417A]/orcl.example.com"
```

!!! note
    The instance name can be found by using the instance parameter as follows `show parameter instance_name`.

!!! note
    Oracle database runs on port 1521. If the port is omitted, the clients will connect to port 1521.

## Local Naming

Local naming stores the service names in file called `tsnames.ora`. This file is located in the **ORACLE_HOME/network/admin** directory. When you create a database using Oracle Database Configuration Assistant (DBCA), local naming is configured automatically.

## Directory Naming

Directory naming resolves a database service, net service name, or net service alias to a connect descriptor stored in an LDAP-compliant directory server

## Check Listener Status

You can use the `lsnrctl` command line tool to check the listner status

```sh
lsnrctl status
```

## Start the listener

```sh
lsnrctl start
```

## Stop the Listener

```sh
lsnrctl stop
```

## Connecting to an Oracle Database from a Client Computer

1. Run the `oraenv` command line tool to configure the environment variables
2. Open `sqlplus`
3. When prompted, enter the user name followed by an at sign (@) and a connect identifier, where the connect identifier has the following format:

`"host[:port][/service_name][:server][/instance_name]"`
