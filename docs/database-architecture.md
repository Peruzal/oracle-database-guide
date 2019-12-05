# Oracle Database Architecture

![Oracle Database Architectures][1]

[1]: images/oracle-database-architecture.png

The oracle database is composed of the following components:

1. The **Oracle software** that you install on your host computer
2. The **database**, which is a collection of physical files on one or more disks
3. The **Oracle instance**, which is composed of the following:
    - The **background processes**, which are the operating system processes or threads that perform the work of accessing, storing, monitoring, and recovering user data, metadata, and control files associated with the database
    - The **shared memory** areas used by the background processes
4. **Server processes** that perform work on behalf of connected users and applications, and the memory and temporary storage used by these processes
5.** Oracle Net**, which is a software layer that enables client applications and Oracle Database to communicate over a network, and the Oracle Net listener, which is a process that listens for connection requests from the network.

