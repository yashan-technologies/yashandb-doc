Transparent Application Failover (TAF) is a technology that allows an application to automatically reconnect or switch to an active instance when a database server fails. Through TAF functionality, the connection switching process requires no human intervention or awareness, but the current statements being executed will be terminated and uncommitted transactions will be rolled back.

TAF belongs to client-side functionality, and developers can choose whether to implement it. By utilizing TAF, businesses can maximize continuity and availability, reduce operational burdens and delays caused by waiting for human intervention, and enhance the end-user experience of applications.

YashanDB has implemented TAF functionality with the following driver interfaces:

- [Transparent Application Failover based on JDBC driver](../../Development Guide/JDBC Driver/YashanDB JDBC Driver Usage Introduction/Transparent Application Failover)
- [Transparent Application Failover based on C driver](../../Development Guide/C Language Family Drivers/C Driver/C Drive Advanced Features/Transparent Application Failover)
