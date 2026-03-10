The SSL connection adds the SSL protocol to a normal TCP connection to establish a [Trusted Channel](../../../Product Security/Encryption/Trusted Channel) for encrypted communication between the database client and server.

All communication through the trusted channel is encrypted, including SQL requests initiated by the client and query result data returned by the server.

YashanDB supports enabling SSL trusted channels for various network links, including [between the database server and client](SSL Trusted Channel Between Database Server and Client), [between HA nodes](SSL Trusted Channel Between HA Nodes), and [between distributed nodes](SSL Trusted Channel Between Distributed Nodes).