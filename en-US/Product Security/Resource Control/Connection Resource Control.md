To ensure that administrators can still connect to the database in case of urgent situations where database resources are exhausted, and perform operations such as KILL SESSION and SHUTDOWN for system recovery or data protection, YashanDB has implemented corresponding controls on connection resources, permanently reserving 2 connection resources for the system.

The conditions for successfully allocating the reserved 2 connections are as follows:

- The system has insufficient resources, and ordinary users can no longer connect to the database.

- The received database login request is in UDS local connection mode.

- The user account initiating the database login request is sys.