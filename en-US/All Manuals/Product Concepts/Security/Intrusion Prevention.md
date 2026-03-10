Establishing a firewall at the database layer is mainly used to detect and defend against external intrusion risks. The following mechanisms can be adopted:

- Establish an IP black/white list. Only login requests initiated by IPs in the whitelist are allowed to connect, while login requests from IPs in the blacklist are denied.

- After enabling connection monitoring, all connection information to YashanDB will be recorded, which helps to detect abnormal connections in a timely manner and take appropriate measures.

## IP Black and White List

YashanDB provides IP access control functionality, allowing or denying remote connection requests from corresponding IPs based on configuration (requests using UDS local connections are not affected), enhancing the security of database access.

The IP black/white list switch is controlled by the TCP.VALIDNODE_CHECKING parameter in the yasdb_net.ini file, which is off by default and the configuration file is not generated automatically. Changes take effect after a restart. To enable it, a configuration file must be created, and the switch parameters and corresponding IP black/white list must be configured.

- Enable and only configure the whitelist: Only remote connection requests to the database from IPs in the whitelist will be allowed.

- Enable and only configure the blacklist: Remote connection requests to the database from IPs in the blacklist will be directly denied.

- Enable and configure both the blacklist and the whitelist: The blacklist takes priority over the whitelist; if the same IP exists in both lists, the blacklist takes effect.

## Connection Monitoring

Connection monitoring is used to detect network attacks such as connection storms. YashanDB achieves this functionality through the recording of monitoring logs.

The monitoring log switch is controlled by the LSNR_LOG parameter, which is enabled by default.

When the connection monitoring log is enabled, all connection requests to YashanDB (regardless of success or failure) will be recorded in the listener.log file, which is automatically created during the installation and deployment of YashanDB. The recorded content in the log file includes SCN, user, connection status, IP, port, etc. This information helps users quickly analyze the source of connections, promptly detect malicious attacks, and take appropriate measures.

## Reserve Connections

YashanDB provides reserve connection functionality to ensure that administrators can still connect to the database in emergency situations where database resources are depleted, allowing actions such as KILL SESSION and SHUTDOWN for system recovery or data protection.

This functionality will always reserve two connection resources for the system, and the conditions for successful allocation of these connections are:

- The system experiences resource shortage, and ordinary users can no longer connect to the database.

- The login request to the database is by UDS local connection method.

- The user account initiating the login request to the database is sys.
