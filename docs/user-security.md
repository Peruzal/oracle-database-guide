# Administering User Security

To connect to the database, a user shoudl be created. Oracle offers different types of user authentication methods. 

## Creating a database user

1. Logon to Oracle as the user with dba roles

```sh
sqlplus / as sysdba
```

2. Create a user with a password `joseph` with the password `P@ssw0rd`

```sh
SQL> create user joseph identified by "P@ssw0rd";
```

3. Tray connect to the database with the created user :

```sh

```

3. The user is created but does not have the right privilege to do anything on the database. We will need to grant the use a role to login on to the database

```sh
SQL > grant create session to joseph;
```

