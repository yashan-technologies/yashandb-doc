The trusted connection refers to establishing a secure, reliable, and encrypted data transmission channel between the application client and YashanDB server, meeting enterprise information security requirements. 

YashanDB C driver has two levels of trusted connection methods:

1. Configure the ssl_root_cer variable in the YashanDB client environment configuration file yasc_env.ini with the CA certificate path. When the application initiates a database connection, it reads this certificate to establish a trusted connection with the database server.
2. Set the CA certificate path matching the server to be connected in the database connection information handle. The subsequent database connections will then read this certificate to establish a trusted connection with the server.

Among these, the second method requires that the encryption protocol type of the certificate configured on the database server be SSL. 

If the application client enables both of the above methods simultaneously, the certificate configured in the current connection information handle will be used preferentially. When neither is enabled, but the database server has enabled the SSL trusted channel, an encrypted communication connection will still be established, but there may be risks such as man-in-the-middle attacks.

##  Configuring the ssl\_root\_cer Variable Method

This method only requires configuring and enabling trusted channels on the database server, and downloading and configuring certificates on the application client to achieve trusted connections. The application itself requires no processing. 

1. The DBA installs SSL or TLCP type certificates on the database server. For operational instructions on certificate installation, please refer to the [certificate management documentation](../../../../产品安全/加密支持/可信信道/管理证书).  If the server already has a certificate, skip this step. 
2. The DBA enables the trusted channel by referring to the server configuration section in the [trusted channel configuration documentation](../../../../产品安全/加密支持/可信信道/配置可信信道). If the server channel is already enabled, skip this step. 
3. The application client administrator downloads the CA root certificate from the database server and saves it locally.
4. The application client administrator refers to the client configuration section in the [trusted channel configuration documentation](../../../../产品安全/加密支持/可信信道/配置可信信道) to add the local certificate path to the YashanDB environment configuration file yasc_env.ini.
5. After completing the above operations, when the application endpoint uses the YashanDB C driver to initiate connections to the server, they will all be established as trusted connections. Please note that when establishing connections, the driver program will verify the local certificate, including correctness and validity. If verification fails, an error will be thrown and the database server connection cannot be established.
6. If a certificate expiration error occurs, please notify the DBA to regenerate the certificate and download it locally, replacing the existing certificate with the new one.

## Setting Connection Information Handle Method

This method still requires configuring and enabling trusted channels on the database server, as well as downloading certificates on the application client, but there is no need to configure the ssl_root_cer variable on the local machine. Multiple sets of CA certificates from different servers can be downloaded, and the corresponding certificate paths can be set for specified servers in the application to establish trusted connections with those servers. When the application client needs to connect to multiple database servers within one connection cycle (such as when TAF is configured) and the certificate information of the servers is not the same, this method can eliminate the limitation that the ssl_root_cer variable can only be a fixed path.

It should be noted that the server certificate downloaded in this method must be a root certificate generated using the SSL encryption protocol, and when the server has a primary-standby topology, the primary-standby trusted channel should be enabled simultaneously.

1. The DBA installs SSL or TLCP type certificates on the database server. For operational instructions on certificate installation, please refer to the [certificate management documentation](../../../../产品安全/加密支持/可信信道/管理证书).  If the server already has a certificate, skip this step. 
2. The DBA enables the trusted channel by referring to the server configuration section in the [trusted channel configuration documentation](../../../../产品安全/加密支持/可信信道/配置可信信道). If the server channel is already enabled, skip this step. 
3. If the server has a primary-standby topology, the DBA needs to refer to the primary-standby replication network trusted channel section in the [trusted channel configuration documentation](../../../../产品安全/加密支持/可信信道/配置可信信道) to enable the primary-standby replication network trusted channel switch. If the server channel is already enabled, skip this step.
4. The application client administrator downloads the CA root certificate from the database server and saves it locally. Multiple server certificates are stored in different paths.
5. The application developer calls the [yacSetConnAttr](../C驱动接口说明/句柄属性设置与查询函数/yacSetConnAttr) interface in the application to set the value of the YAC_ATTR_SSL_ROOT_CER attribute to the path of the corresponding CA root certificate for this server on the local machine.  Example statement as follows: 
   ```c
   const YacChar* gSrvStr = "192.168.1.2:1688?FAILOVER = ON & FAILOVER_TYPE = SESSION & FAILOVER_RETRIES = 10 & FAILOVER_DELAY = 1";
   const YacChar* user = "sys";
   const YacChar* pwd = "password";
   const YacChar* sslpath = "/data/yashan/ssl/root.crt";
    
   YacHandle env;
   YacHandle conn;
   YAC_CALL(yacAllocHandle(YAC_HANDLE_ENV, NULL, &env));
   YAC_CALL(yacAllocHandle(YAC_HANDLE_DBC, env, &conn));
   YAC_CALL(yacSetConnAttr(conn, (YacConnAttr)YAC_ATTR_SSL_ROOT_CER, (YacVoid*)sslpath, sizeof(CodPointer)))
   YAC_CALL(yacConnect(conn, gSrvStr, YAC_NULL_TERM_STR, user, YAC_NULL_TERM_STR, pwd, YAC_NULL_TERM_STR));
   ```

   The above example statement can establish a trusted connection with the server. After setting the YAC_ATTR_SSL_ROOT_CER attribute value, the driver program will ignore the ssl_root_cer variable value and use the CA certificate from the set path to perform communication authentication with the database server. If the certificate is incorrect or expired, an error will be thrown and the database connection will fail. 

 6. The application developer calls the [yacDisconnect](../C驱动接口说明/连接、授权和初始化函数/yacDisconnect) interface in the application to disconnect the database connection. At this point, the driver program will clear the YAC_ATTR_SSL_ROOT_CER attribute value set by yacSetConnAttr.