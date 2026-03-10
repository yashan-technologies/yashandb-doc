The Distributed Inter-node Communication Network (DIN) supports SSL trusted channels.

When distributed nodes communicate across nodes, they can either be the active connecting party (considered as the SSL handshake client) or the passive connecting party (considered as the SSL handshake server). Therefore, each node needs to configure relevant parameters such as the server private key and server certificate.

> **Caution**: 
>
> - The internal communication network between distributed nodes must uniformly enable the SSL trusted channel across the entire cluster; partial node configuration or inconsistent configurations are not allowed.
>
> - All distributed nodes' server certificates should be signed with the same root certificate; otherwise, an error will occur.
>
> - If the SSL trusted channel switch is enabled but the certificate path is not configured or is configured incorrectly, the database will fail to start.

## Tool Preparation

Generating certificates requires the use of the OpenSSL tool. Please refer to [Dependency Preparation](../../../Installation and Upgrade/Installation and Deployment/Pre-Installation Preparation/Preparing Dependencies) to check and ensure that the required tools are installed on the server system.

## Generate Certificates

The command options, paths, names, etc. in the following steps are flexible; users can replace them with values that suit their environment and needs.

To simplify operations, this manual will manage the server private key and certificate on a per-server basis, meaning that all distributed nodes running on the same server share the same server certificate, which is stored in the $YASDB_HOME/ssl directory. At the same time, the root certificate, server private key, server certificate, and DH files are generated only once and copied among all cluster nodes.

1. Generate Root Certificate (self-signed, includes server public key):

   The root certificate is used for signing certificates of distributed nodes, and the entire distributed cluster should use the same root certificate.

   ```shell
   $ openssl req -new -x509 -days 365 -nodes -out root.crt -keyout ca.key -subj "/CN=RootCA"
   ```

2. Generate Root Certificate Signing Request File and Server Private Key:

   ```shell
   $ openssl req -new -nodes -text \
   -out server.csr \
   -keyout server.key \
   -subj "/CN=server"
   ```

3. Generate and Sign the Server Certificate:

   ```shell
   $ openssl x509 -req -in server.csr -text -days 5 \
   -CA root.crt \
   -CAkey ca.key \
   -CAcreateserial \
   -out server.crt
   ```

4. Generate DH File:

   ```shell
   $ openssl dhparam -2 -out dhparam.pem -text 2048
   ```

5. Copy the Certificate to the $YASDB_HOME/ssl directory of other servers.

   In practice, users can balance their needs for certificate management convenience and security to adjust the configuration process and methods.

   If managing certificates on a per-node basis is desired, the certificates can be stored in the $YASDB_DATA/config/ssl directory and copied according to nodes, with each distributed node using its own certificate.

   To enhance security, when configuring another certificate management unit (server or node), only copy the root certificate and execute steps 2 - 4 to generate the server private key and certificate separately.

> **Note**: 
>
> If the root certificate or server certificate expires, it needs to be regenerated.

## Configuration Parameters

1. Enable the SSL trusted channel switch in the database and configure the certificate paths. Note that the paths may only be specified as absolute paths and must not exceed 254 bytes in length. Users can replace the example certificate paths with values that suit their environment and needs.

   ```sql
   ALTER SYSTEM SET din_ssl_enable = ON SCOPE=spfile;
   ALTER SYSTEM SET ssl_cert_file = /home/yashan/YASDB_HOME/ssl/server.crt SCOPE=spfile;
   ALTER SYSTEM SET ssl_dh_param_file = /home/yashan/YASDB_HOME/ssl/dhparam.pem SCOPE=spfile;
   ALTER SYSTEM SET ssl_key_file = /home/yashan/YASDB_HOME/ssl/server.key SCOPE=spfile;
   ```

2. After confirming that all distributed nodes have completed certificate generation and parameter configuration correctly, restart the database.