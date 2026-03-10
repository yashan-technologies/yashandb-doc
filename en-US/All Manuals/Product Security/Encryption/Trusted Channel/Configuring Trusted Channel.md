Before configuring trusted channels, please first understand their [configuration rules](00Trusted Channel) and select the trusted channels to be configured and the appropriate encryption protocol based on actual requirements.

After configuring trusted channels, subsequent attention should be paid to the validity period of the corresponding certificates, and certificates should be [replaced](./管理证书.html#expire) in a timely manner before they expire.

##  Client \<-> Server Trusted Channel

The database client \<-> server trusted channel supports using either [SSL protocol](#ssl) or [TLCP protocol](#tlcp), and one can be optionally selected as needed.

To enable the database client \<-> server trusted channel, correct server certificate files that are within their validity period must be stored on each database server and the relevant database configuration must be completed. Correct client certificate files that are within their validity period should also be stored and configured on each device that needs to use clients/drivers to connect to the database.

> **Note**:
>
> When using the SSL protocol, configuring the corresponding certificate on client/driver devices is optional.
>
> - If configured, it must be configured as correct CA root certificate files that are within their validity period. 
>
> - If not configured (i.e., unidirectional SSL trusted channel, **not recommended**), it does not affect encryption communication capabilities, but there may be risks such as man-in-the-middle attacks.


<span id="ssl" name="ssl" class="yaslink"></span>

###  Using SSL Protocol



#### Preparation

- [SSL protocol related certificates](./管理证书.html#getSSLCert) have been generated.

- The storage path for certificate files has been planned and created on each database server. The path for each file must be consistent, and the YashanDB installation user (i.e., yashan) must have full permissions for the corresponding path. This article uses /data/yashan/ssl path as a unified example.

    ```shell
    $ cd /data/yashan
    $ mkdir ssl
    ```

- Configuring the trusted channel requires restarting the database.



#### Step 1: Server Configuration

1. Use Linux commands to copy the required server certificate files to the specified path on each database server.

2. Connect and log in to the database as a DBA user.
    ```shell
    $ yasql sales/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```


3. Enable the SSL trusted channel switch and configure the server certificate paths.

    ```sql
    ALTER SYSTEM SET SSL_ENABLE = ON SCOPE=spfile;
    ALTER SYSTEM SET SSL_CERT_FILE = /data/yashan/ssl/server.crt SCOPE=spfile;
    ALTER SYSTEM SET SSL_DH_PARAM_FILE = /data/yashan/ssl/dhparam.pem SCOPE=spfile;
    ALTER SYSTEM SET SSL_KEY_FILE = /data/yashan/ssl/server.key SCOPE=spfile;
    ```

    > **Warn**:
    >
    > If the SSL trusted channel switch is enabled but the certificate path is not configured or is configured incorrectly, the database cannot start.

4. Restart the database to make the configuration take effect.

#### Step 2: Client Configuration

This chapter introduces client configuration using the Linux platform as an example.

1. Download the CA root certificate to a local path, for example, /home/yasdb/cert.

2. Create a client folder in the YashanDB client directory and create an empty file named yasc_env.ini within the client folder.

    ```shell
    cd /home/yasdb/yashandb_client
    $ mkdir client
    $ cd client
    $ vi yasc_env.ini
    ```

3. Set environment variables.

    ```shell
    $ vi ~/.bashrc
    export YASDB_HOME=/home/yasdb/yashandb_client
    $ source ~/.bashrc
    ```

    > **Note**: 
    >
    > The YASDB_HOME path must point to the YashanDB client folder.

4. Add the following configuration to ${YASDB_HOME}/client/yasc_env.ini:

    ```ini
    ssl_root_cer = /home/yasdb/cert/root.crt
    ```

    In this configuration, the path can be specified as an absolute or relative path, but it cannot be a relative path containing ../, and the system reads the file according to the first 255 bytes length.

<span id="tlcp" name="tlcp" class="yaslink"></span>

###  Using TLCP Protocol

#### Preparation

- [TLCP protocol related certificates](./管理证书.html#getTLCPCert) have been generated.

- The storage path for certificate files has been planned and created on each database server. The path for each file must be consistent, and the YashanDB installation user (i.e., yashan) must have full permissions for the corresponding path. This article uses /data/yashan/tlcp path as a unified example.

    ```shell
    $ cd /data/yashan
    $ mkdir tlcp
    ```

- Configuring the trusted channel requires restarting the database.

#### Step 1: Server Configuration

1. Use Linux commands to copy the required server certificate files to the specified path on each database server.

2. Connect and log in to the database as a DBA user.
    ```shell
    $ yasql sales/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```


3. Enable the TLCP trusted channel switch and configure the server certificate paths.

    ```sql
    ALTER SYSTEM SET SSL_ENABLE = ON SCOPE=spfile;
    ALTER SYSTEM SET ENCRYPT_TYPE = tlcp SCOPE=spfile;
    ALTER SYSTEM SET TLCP_CERT_FILE = /data/yashan/tlcp/double_certs.pem SCOPE=spfile;
    ALTER SYSTEM SET TLCP_SIGNKEY_FILE = /data/yashan/tlcp/signkey.pem SCOPE=spfile;
    ALTER SYSTEM SET TLCP_ENCKEY_FILE = /data/yashan/tlcp/enckey.pem SCOPE=spfile;
    ALTER SYSTEM SET TLCP_CACERT_FILE = /data/yashan/tlcp/cacert.pem SCOPE=spfile;
    ALTER SYSTEM SET TLCP_PASS_FILE = /data/yashan/tlcp/tlcpPass SCOPE=spfile;
    ```

    > **Warn**:
    >
    > If the TLCP Trusted Channel is enabled but the certificate path is not configured or the path is incorrect, the database will not start.

4. Restart the database to make the configuration take effect.

#### Step 2: Client Configuration

This document provides client configuration based on the Linux platform as an example.

1. Download the server root certificate to a local path, such as /home/yasdb/cert.

2. Create a client folder in the YashanDB client folder and create an empty file named yasc_env.ini in the client folder.

    ```shell
    cd /home/yasdb/yashandb_client
    $ mkdir client
    $ cd client
    $ vi yasc_env.ini
    ```

3. Set environment variables.

    ```shell
    $ vi ~/.bashrc
    export YASDB_HOME=/home/yasdb/yashandb_client
    $ source ~/.bashrc
    ```

    > **Note**: 
    >
    > The YASDB_HOME path must point to the YashanDB client folder.

4. Add the following configuration in ${YASDB_HOME}/client/yasc_env.ini:

    ```ini
    TLCP_CACERT_FILE=/home/yasdb/cert/rootcacert.pem
    TLCP_CERT_FILE=/home/yasdb/cert/clientcert.pem
    TLCP_CLIENT_KEY_FILE=/home/yasdb/cert/clientkey.pem
    TLCP_CLIENT_KEY_PASS = 1234
    ```

    The paths in the configuration can be specified as absolute or relative paths, but cannot contain relative paths with ../, and the system reads files according to the first 255 bytes in length.

<span id="hassl" name="hassl" class="yaslink"></span>

##  Primary-standby Replication Network Trusted Channel

Primary-standby replication network trusted channel supports the following methods:

- Using database client \<-> server listener: For configuration operations, please refer to [Client \<-> Server SSL Trusted Channel](#ssl).

- Primary-standby node self-listening: Correct server certificate files that are within their validity period must be stored on each database server, and corresponding configuration must be completed. For specific configuration methods, see below.



#### Preparation

- [SSL protocol related certificates](./管理证书.html#getSSLCert) have been generated.

- The storage path for certificate files has been planned and created on each database server. The path for each file must be consistent, and the YashanDB installation user (i.e., yashan) must have full permissions for the corresponding path. This article uses /data/yashan/ssl path as a unified example.

    ```shell
    $ cd /data/yashan
    $ mkdir ssl
    ```

- Configuring the trusted channel requires restarting the database.



#### Operating Steps

1. Use Linux commands to copy the required server certificate files to the specified path on each database server.

2. Connect and log in to the database as a DBA user.
    ```shell
    $ yasql sales/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```


3. Enable the primary-standby replication network trust channel switch and configure the server certificate paths.

    ```sql
    ALTER SYSTEM SET HA_SSL_ENABLE = ON SCOPE=spfile;
    ALTER SYSTEM SET SSL_CERT_FILE = /data/yashan/ssl/server.crt SCOPE=spfile;
    ALTER SYSTEM SET SSL_DH_PARAM_FILE = /data/yashan/ssl/dhparam.pem SCOPE=spfile;
    ALTER SYSTEM SET SSL_KEY_FILE = /data/yashan/ssl/server.key SCOPE=spfile;
    ```

4. Restart the database to make the configuration take effect.

<span id="dinssl" name="dinssl" class="yaslink"></span>

##  DIN Trusted Channel

To simplify operations, this article manages server private keys and certificates on a per-server basis, using the example where all distributed nodes running on the same server share the same server certificate.

In actual usage, the configuration process and methods can be adjusted according to requirements for certificate management convenience and security. For example, certificates can be managed on a per-node basis, or a single root certificate can be shared to generate different server certificates for different node groups.



#### Preparation

- [SSL protocol related certificates](./管理证书.html#getSSLCert) have been generated.

- The storage path for certificate files has been planned and created on each database server. The path for each file must be consistent, and the YashanDB installation user (i.e., yashan) must have full permissions for the corresponding path. This article uses /data/yashan/ssl path as a unified example.

    ```shell
    $ cd /data/yashan
    $ mkdir ssl
    ```

- Configuring the trusted channel requires restarting the database.



#### Operating Steps

1. Use Linux commands to copy the required server certificate files to the specified path on each database server.

2. Connect and log in to the database as a DBA user.
    ```shell
    $ yasql sales/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```


3. Enable the primary-standby replication network trust channel switch and configure the server certificate paths.

    ```sql
    ALTER SYSTEM SET DIN_SSL_ENABLE = ON SCOPE=spfile;
    ALTER SYSTEM SET SSL_CERT_FILE = /data/yashan/ssl/server.crt SCOPE=spfile;
    ALTER SYSTEM SET SSL_DH_PARAM_FILE = /data/yashan/ssl/dhparam.pem SCOPE=spfile;
    ALTER SYSTEM SET SSL_KEY_FILE = /data/yashan/ssl/server.key SCOPE=spfile;
    ```

4. Restart the database to make the configuration take effect.
