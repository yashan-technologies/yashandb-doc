YashanDB provides the following two ways to establish a connection:

- Remote Connection: The client communicates with the server over the network using the TCP/IP protocol. When using a remote connection, it is necessary to specify the database URL (server address + listening port). The IP address supports formats such as IPv4, IPv6, and domain names.

- UDS Local Connection: The client and server are on the same server, using the IPC protocol for inter-process communication. No database URL needs to be specified for UDS local connections, which default to connecting to the database corresponding to the local $YASDB_DATA environment variable.
    
In certain scenarios, YashanDB will restrict users to log in to the database only via UDS local connections, such as:
    
- [OS Authentication](../Identity Identification and Authentication/OS Authentication/00OS Authentication)

- [Reserved Connection](../Resource Control/Connection Resource Control) in emergencies when connection resources are exhausted

Physically isolating users from the database server is the most basic security requirement. Therefore, except in special circumstances, users should log in and operate the database using remote connections. At the same time, mechanisms can be established to detect and defend against external intrusions to further enhance database security:

- [IP Blacklist and Whitelist Management](IP Blacklist and Whitelist Management): Control access to login requests initiated by IPs on the list through whitelisting or blacklisting.

- [Connection Listening](Connection Listening): Records all connection information to YashanDB, allowing timely detection of abnormal connections and implementation of measures.