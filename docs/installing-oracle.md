# Installing Oracle Database

Oracle provides the Oracle Universal Installed(OUI) to install and configure an oracle database.

## Checking the prerequisites

The following are the minimum requirements to install the oracle database :

- There is a minimum of 1 GB of physical memory.
- Sufficient paging space is available.
- The appropriate service packs or patches for your operating system are installed.
- An appropriate file system format is being used.


## Database Editions

The oracle software is bundled as a single product, you will need to choose the right edition for licensing during the installation phase. The following editions are available:

1. Enterprise Edition
2. Standard Edition
3. Standard Edition One
4. Personal Edition

###  Enterprise Edition

This installation type is the full-featured Oracle Database product that provides data management for enterprise-level applications. It is intended for mission-critical, high-security online transaction processing (OLTP) and data warehousing environments.

### Standard Edition

This installation type is suitable for workgroup or department-level applications, and for small to medium-sized enterprises. It provides core relational database management services and options and includes an integrated set of management tools, replication, Web features, and facilities for building business-critical applications

### Standard Edition One

This installation type is suitable for workgroup, department, or web applications. It provides core relational database management services for single-server environments or highly distributed branch environments. Oracle Standard Edition One includes all the facilities necessary to build business-critical applications.

### Personal Edition

(Microsoft Windows operating systems only)—This installation type installs the same software as the Enterprise Edition, but supports only a single-user, development and deployment environment.

## Database Directories

You will need to specify the database directory in which the oracle software is installed. The base directory is called the **Oracle Base**.

## Database File Locations

Oracle offers a choice of two ways to manage the database files :

1. File System
2. Oracle Managed Files

### File System

This default option creates database files that are managed by the file system of your operating system. You can specify the directory path where database files are to be stored. Oracle Database can create and manage the actual files.

### Oracle Managed Files - OMF

This option enables you to place your data files in Oracle Automatic Storage Management (Oracle ASM) disk groups. 

Oracle Database automatically manages database file placement and naming. For environments with a large number of disks, this option simplifies database administration and maximizes performance. Oracle ASM performs software striping and mirroring at the file level for maximum storage flexibility, performance, and availability.

## Database Name

The **oracle system identifier(SID)** is a unique name that is used to distinguish this instance of oracle from other database instances that might installed on the same machine.

The **global database name** is the full name of the database that uniquely distinguishes it from any other database. The global database name is in the form **database_name.database_domain**, for example **sales.example.com**. The database name portion sales is a simple name you call your database.

## Installing Oracle on Centos 7

Download the oracle database from the[Oracle website][1]

[1]: https://www.oracle.com/database/technologies/oracle-database-software-downloads.html

## Unpack the files

```sh
unzip linuxx64_12201_database.zip 
```

You should now have a single directory called "database" containing installation files.

## Hosts file

Change the machine hostname. Change it in the `/etc/hosts`

```sh
<IP-address>  <fully-qualified-machine-name>  <machine-name>
```

For example, the machine is called `mid-earth`

```sh
127.0.0.1       localhost localhost.localdomain localhost4 localhost4.localdomain4 mid-earth
192.168.0.215   ol7.localdomain  mid-earth
```

Set the correct hostname in the "/etc/hostname" file.

```sh
mid-earth
```

## Oracle Installation Prerequisites

Perform either the Automatic Setup or the Manual Setup to complete the basic prerequisites. The Additional Setup is required for all installations.

## Automatic Setup

If you plan to use the "oracle-rdbms-server-12cR1-preinstall" package to perform all your prerequisite setup, issue the following command.

```sh
yum install oracle-rdbms-server-12cR1-preinstall -y
```

!!! note
    Earlier versions of Oracle Linux required manual setup of the Yum repository by following the instructions at http://public-yum.oracle.com.


It is probably worth doing a full update as well, but this is not strictly speaking necessary.

```sh
yum update -y
```

## Manual Setup

If you have not used the `oracle-rdbms-server-12cR1-preinstal` package to perform all prerequisites, you will need to manually perform the following setup tasks.

Add the following lines to the `/etc/sysctl.conf` file, or in a file called `/etc/sysctl.d/oracle.conf`.

```sh
fs.file-max = 6815744
kernel.sem = 250 32000 100 128
kernel.shmmni = 4096
kernel.shmall = 1073741824
kernel.shmmax = 4398046511104
kernel.panic_on_oops = 1
net.core.rmem_default = 262144
net.core.rmem_max = 4194304
net.core.wmem_default = 262144
net.core.wmem_max = 1048576
net.ipv4.conf.all.rp_filter = 2
net.ipv4.conf.default.rp_filter = 2
fs.aio-max-nr = 1048576
net.ipv4.ip_local_port_range = 9000 65500
```

Run one of the following commands to change the current kernel parameters, depending on which file you edited.

```sh
/sbin/sysctl -p
# Or
/sbin/sysctl -p /etc/sysctl.d/oracle.conf
```

Add the following lines to a file called `/etc/security/limits.d/oracle.conf` file.

```sh
oracle   soft   nofile    1024
oracle   hard   nofile    65536
oracle   soft   nproc    16384
oracle   hard   nproc    16384
oracle   soft   stack    10240
oracle   hard   stack    32768
oracle   hard   memlock    134217728
oracle   soft   memlock    134217728
```

The following packages are listed as required, including the 32-bit version of some of the packages. Many of the packages should be installed already.

```sh
yum install binutils -y
yum install compat-libstdc++-33 -y
yum install compat-libstdc++-33.i686 -y
yum install gcc -y
yum install gcc-c++ -y
yum install glibc -y
yum install glibc.i686 -y
yum install glibc-devel -y
yum install glibc-devel.i686 -y
yum install ksh -y
yum install libgcc -y
yum install libgcc.i686 -y
yum install libstdc++ -y
yum install libstdc++.i686 -y
yum install libstdc++-devel -y
yum install libstdc++-devel.i686 -y
yum install libaio -y
yum install libaio.i686 -y
yum install libaio-devel -y
yum install libaio-devel.i686 -y
yum install libXext -y
yum install libXext.i686 -y
yum install libXtst -y
yum install libXtst.i686 -y
yum install libX11 -y
yum install libX11.i686 -y
yum install libXau -y
yum install libXau.i686 -y
yum install libxcb -y
yum install libxcb.i686 -y
yum install libXi -y
yum install libXi.i686 -y
yum install make -y
yum install sysstat -y
yum install unixODBC -y
yum install unixODBC-devel -y
yum install zlib-devel -y
yum install zlib-devel.i686 -y
```

Create the new groups and users.

```sh
groupadd  oinstall
groupadd  dba
groupadd  oper
#groupadd backupdba
#groupadd dgdba
#groupadd kmdba
#groupadd asmdba
#groupadd asmoper
#groupadd asmadmin

useradd -g oinstall -G dba,oper oracle
```

Uncomment the extra groups you require.

## Additional Setup


The following steps must be performed, whether you did the manual or automatic setup.

Set the password for the "oracle" user.

```sh
passwd oracle
```

Set secure Linux to permissive by editing the `/etc/selinux/config` file, making sure the SELINUX flag is set as follows.

```sh
SELINUX=permissive
```

Once the change is complete, restart the server or run the following command.

```sh
setenforce Permissive
```

If you have the Linux firewall enabled, you will need to disable or configure it. To disable it, do the following.

```sh
# systemctl stop firewalld
# systemctl disable firewalld
```

Create the directories in which the Oracle software will be installed.

```sh
mkdir -p /u01/app/oracle/product/12.1.0.2/db_1
chown -R oracle:oinstall /u01
chmod -R 775 /u01
```

!!! note
    Putting mount points directly under root is typically a bad idea. It's done here for simplicity, but for a real installation "/" should be reserved for the OS.

Unless you are working from the console, or using SSH tunnelling, login as root and issue the following command.

```sh
xhost +<machine-name>
```

Add the following lines at the end of the "/home/oracle/.bash_profile" file.

```sh
# Oracle Settings
export ORACLE_HOSTNAME=mid-earth
export ORACLE_BASE=/u01/app/oracle
export ORACLE_HOME=$ORACLE_BASE/product/12.2.0.2/db_1

export PATH=/usr/sbin:$PATH
export PATH=$ORACLE_HOME/bin:$PATH

```

## Start the Database Installation

Log into the oracle user. If you are using X emulation then set the DISPLAY environmental variable.

```sh
DISPLAY=<machine-name>:0.0; export DISPLAY
```

Start the Oracle Universal Installer (OUI) by issuing the following command in the database directory.

```sh
./runInstaller
```

Proceed with the installation of your choice.

!!! note
    If you are doing an installation for an Enterprise Manager repository, remember to do an advanced installation and pick the ALT32UTF8 character set.

The graphical installation will start and show the following steps


- Configure Security Updates
- My Oracle Support Credentials
- Select Installation Type
- System Class
- Grid Installation Options
- Select Install Type
- Typical Install Configuration
- Create Inventory
- Perform Prerequisite Checks
- Summary
- Install Product
- Execute Configuration Scripts
- Oracle Database Configuration
- Database Configuration Assistant
- Database Configuration Assistant Complete
- Finish
- Database Express 12c Login
- Database Express 12c Dashboard

## Creating a Database

1. Open a command prompt as the user `oracle`

2. Run dbca

```sh
dbca
```

Follo the prompts to create a new oracle database.

## Post Installation

Edit the `/etc/oratab` file setting the restart flag for each instance to 'Y'.

```sh
orcl:/u01/app/oracle/product/12.2.0.2/db_1:Y
```


## Connect to Oracle Enterprise Manager Express

To get up and running, you just need to check the HTTPS port is set for the XML DB.

```sh
SQL> SELECT DBMS_XDB_CONFIG.gethttpport FROM dual;

GETHTTPPORT
-----------
          0

SQL> SELECT DBMS_XDB_CONFIG.gethttpsport FROM dual;

GETHTTPSPORT
------------
        5500

SQL> EXEC DBMS_XDB_CONFIG.sethttpsport(5500);

PL/SQL procedure successfully completed.

SQL>
```

Once that is done, EM Database Express is accessible using the following type of URL.

```sh
https://<hostname>:<port>/em/

Example:

https://localhost:5500/em/
```

Login in with the sys user.

## Setting Up the Oracle Environment

On a Linux machine you can use a script in `/usr/local/bin/oraenv` to setup the environment variables.

## Granting Access to EM Express for Nonadministrative Users

To connect to EM, the users need to be granted the `EM_EXPRESS_BASIC` or `EM_EXPRESS_ALL` roles.

```sh
sql > grant EM_EXPRESS_ALL to jsmith;
```

## Block Size

The `db_block_size` size parameter shows the smallest unit of storage on the file system for the tablespaces. This parameter can not be changed after the database is created. The default size is 8K. 

```sh
show parameter db_block_size
```

