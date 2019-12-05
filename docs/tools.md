# Tools for managing the database

The following are tools used to manage the Oracle database :

1. Oracle Universal Installer
2. Oracle Database Configuration Assistant
3. Database Upgrade Assistant
4. Net Configuration Assistant
5. Oracle Enterprise Manager Database Express
6. SQL Developer

## Oracle Universal Installer - oui

![Oracle Universal Installer][1]

Oracle Universal Installer (OUI) is a utility that installs your Oracle software and options. It can automatically start Oracle Database Configuration Assistant to install a database.

## Oracle Database Configuration Assistant - dbca

![Oracle Database Configuration Assistance][2]

Oracle Database Configuration Assistant (DBCA) is a utility that creates a database from templates that are supplied by Oracle, or you can create your own.

## Database Upgrade Assistant

The Database Upgrade Assistant (DBUA) is a tool that guides you through the upgrade of your existing database to a new Oracle Database release.

## Net Configuration Assistant - netca

Net Configuration Assistant is a utility that enables you to configure listeners and naming methods, which are critical components of the Oracle Database network.

## Oracle Enterprise Manager Database Express

The primary product for managing your database is Oracle Enterprise Manager Database Express (EM Express), a Web-based interface.

## SQL Developer

![SQL Developer][3]

SQL Developer provides another GUI for accessing your Oracle database. 


## SQL Plus

SQL Plus is a command line client program for managing a Linux database.

## To start SQL*Plus and connect to the database from the command line:



1. Open a command window.

2. Configure the operating system environment variables.

3. Start SQL*Plus using a command in the following format:


```sh
sqlplus {username | /} [as sysdba]
```

An example of this command is:

```sh
$ sqlplus / AS SYSDBA
Enter password: password
```

For username, you can use the **SYS** or **SYSTEM** administrative users. At the prompt, enter the password that you set up during installation. If you use the SYS user, you must include **AS SYSDBA** after the username.

!!! note
    SQL*Plus connects you to the default database instance (Microsoft Windows) or the database instance specified by environment variables (Linux and UNIX).



[1]: images/oui.png
[2]: images/dbca.jpg
[3]: images/sql-developer.png