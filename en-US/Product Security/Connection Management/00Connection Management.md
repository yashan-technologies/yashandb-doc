##  Connection Methods

YashanDB provides the following two ways to establish a connection:

- Remote Connection: The client communicates with the server over the network using the TCP/IP protocol. When using a remote connection, it is necessary to specify the database URL (server address + listening port). The IP address supports formats such as IPv4, IPv6, and domain names.

- UDS Local Connection: The client and server are on the same server, using the IPC protocol for inter-process communication. No database URL needs to be specified for UDS local connections, which default to connecting to the database corresponding to the local $YASDB_DATA environment variable.
    
In certain scenarios, YashanDB will restrict users to log in to the database only via UDS local connections, such as:
    
    - [OS Authentication](../Identity Identification and Authentication/OS Authentication/00OS Authentication)

    - [Reserved connection](#Reservation) in emergencies when connection resources are exhausted

    - After setting the connection listener to maintenance mode, only local connections are accepted

Physically isolating users from the database server is the most basic security requirement. Therefore, except in special circumstances, users should log in and operate the database using remote connections. At the same time, mechanisms can be established to detect and defend against external intrusions to further enhance database security:

- [Managing Connection Listener](./Managing Connection Listener): By adjusting the operating mode of the connection listener, whether to accept new connections can be controlled as needed, achieving connection management without stopping the database. By configuring to record connection listener logs (recorded by default), abnormal connections can be promptly detected and security audit requirements can be met.

- [Managing IP Blacklist and Whitelist](./Managing IP Blacklist or Whitelist): Control access to login requests initiated by IPs on the list through whitelisting or blacklisting.

- [Controlling Session Resources](./Controlling Session Resources): Session resource controls, such as limiting the number of parallel connections and session idle time, can effectively reduce the security risks posed by idle connections, prevent malicious attacks and potential data leaks, avoid resource abuse, and improve the performance and stability of the database.

<span id="Reservation" name="Reservation"></span>

##  Emergency Connection Resource Reservation

YashanDB prevents the database from being unresponsive to legitimate requests due to exhaustion of connection resources during heavy load, DoS attacks, or connection floods by always maintaining a certain number of available connections. This significantly enhances the security, stability, and reliability of the system.

The emergency connection resource reservation mechanism can ensure that administrators can still connect to the database in emergency situations where database resources are exhausted, to perform system recovery or data protection operations such as KILL SESSION, SHUTDOWN, etc.

YashanDB always reserves 2 emergency connection resources for the system. The prerequisite conditions for successful allocation of these resources are as follows:

- The system has insufficient resources, and ordinary users can no longer connect to the database.

- The received database login request is in UDS local connection mode.

- The user initiating the database login request is sys.
