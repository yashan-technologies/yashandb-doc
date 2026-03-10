YashanDB enables SSL trusted channel connection, which requires the server to generate a root certificate, server certificate, and DH file. The client obtains the root certificate from the server and performs security verification from client to server during communication.

> **Caution**: 
>
> - Once the server activates the SSL trusted channel, all clients must have the root certificate to connect to the database.
>
> - Enabling SSL trusted channel is not related to user password authentication; users still need to enter a password to log in to the database.

## Server Configuration

### Tool Preparation

To generate certificates, the OpenSSL tool must be used. Please refer to [Preparing Dependencies](../../../Installation and Upgrade/Installation and Deployment/Pre-Installation Preparation/Preparing Dependencies) to check and ensure that the necessary tools are installed on the server system.

### Generate Certificates

In the following steps, the tool command options, paths, names, etc., are unrestricted. Users can replace them with other values according to their own environment and needs.

1. Generate the root certificate (self-signed, includes server public key):

    The root certificate is used to sign the database server certificate and sent to the client for SSL trusted channel verification. This manual uses the path /home/yashan/YASDB_DATA/config as an example.

    ```shell
    $ openssl req -new -x509 -days 365 -nodes -out root.crt -keyout ca.key -subj "/CN=RootCA"
    ```

2. Generate the root certificate request file and server private key:

    ```shell
    $ openssl req -new -nodes -text \
    -out server.csr \
    -keyout server.key \
    -subj "/CN=server"
    ```

3. Generate and sign the server certificate:

    ```shell
    $ openssl x509 -req -in server.csr -text -days 5 \
    -CA root.crt \
    -CAkey ca.key \
    -CAcreateserial \
    -out server.crt
    ```

4. Generate the DH file:

    ```shell
    $ openssl dhparam -2 -out dhparam.pem -text 2048
    ```

> **Note**: 
>
> If the root certificate or server certificate expires, a new certificate must be generated.

### Configuration Parameters

1. Enable the SSL trusted channel switch in the database and configure the certificate paths. The paths must be absolute and no longer than 254 bytes.

    ```sql
    ALTER SYSTEM SET ssl_enable = ON SCOPE=spfile;
    ALTER SYSTEM SET ssl_cert_file = /home/yashan/YASDB_DATA/config/server.crt SCOPE=spfile;
    ALTER SYSTEM SET ssl_dh_param_file = /home/yashan/YASDB_DATA/config/dhparam.pem SCOPE=spfile;
    ALTER SYSTEM SET ssl_key_file = /home/yashan/YASDB_DATA/config/server.key SCOPE=spfile;
    ```

    > **Warn**:
    >
    > If the SSL trusted channel switch is enabled but the certificate path is not configured or is configured incorrectly, the database cannot start.

2. Restart the database.

## Client Configuration

This manual introduces client configuration using the Linux platform as an example.

1. Download the server root certificate to a local path, for example, /home/yasdb/cert.

2. Create a client folder in the YashanDB client directory and create an empty file named yasc_env.ini within the client folder.

    ```shell
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