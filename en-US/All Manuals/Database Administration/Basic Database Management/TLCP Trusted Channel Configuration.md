YashanDB enables the [TLCP Trusted Channel](../../Product Security/Encryption/Trusted Channel.html#tlcp) connection, which requires the Gmssl tool to generate the relevant certificates. After correctly configuring the certificates, secure verification occurs from the client to the server during communication.

> **Caution**: 
>
> - Once the server enables the TLCP Trusted Channel, all clients must correctly configure certificates to connect to the database.
>
> - The enabling of the TLCP Trusted Channel is unrelated to user password authentication; users still need to enter a password to log into the database.

## Server Configuration

### Tool Preparation

To generate certificates, the GmSSL tool must be used. Please refer to the [Preparing Dependencies](../../Installation and Upgrade/Installation and Deployment/Pre-Installation Preparation/Preparing Dependencies) section to check and ensure that the required tools are installed on the server system.

### Generating Certificates

In the following steps, the tool command options, paths, names, etc. are unrestricted, and users may replace them with other values according to their environment and needs.

1. Generate the server certificate:

    - Generate the root certificate

        ```shell
        $ gmssl sm2keygen -pass 1234 -out rootcakey.pem
        $ gmssl certgen -C CN -ST Beijing -L Haidian -O PKU -OU CS -CN ROOTCA -days 3650 -key rootcakey.pem -pass 1234 -out rootcacert.pem -key_usage keyCertSign -key_usage cRLSign -ca
        $ gmssl certparse -in rootcacert.pem
        ```

    - Generate the CA certificate

        ```shell
        $ gmssl sm2keygen -pass 1234 -out cakey.pem
        $ gmssl reqgen -C CN -ST Beijing -L Haidian -O PKU -OU CS -CN "Sub CA" -key cakey.pem -pass 1234 -out careq.pem
        $ gmssl reqsign -in careq.pem -days 365 -key_usage keyCertSign -path_len_constraint 0 -cacert rootcacert.pem -key rootcakey.pem -pass 1234 -out cacert.pem -ca
        $ gmssl certparse -in cacert.pem
        ```

    - Use the CA certificate to issue a signing certificate

        ```shell
        $ gmssl sm2keygen -pass 1234 -out signkey.pem
        $ gmssl reqgen -C CN -ST Beijing -L Haidian -O PKU -OU CS -CN localhost -key signkey.pem -pass 1234 -out signreq.pem
        $ gmssl reqsign -in signreq.pem -days 365 -key_usage digitalSignature -cacert cacert.pem -key cakey.pem -pass 1234 -out signcert.pem
        $ gmssl certparse -in signcert.pem
        ```

    - Use the CA certificate to issue an encryption certificate

        ```shell
        $ gmssl sm2keygen -pass 1234 -out enckey.pem
        $ gmssl reqgen -C CN -ST Beijing -L Haidian -O PKU -OU CS -CN localhost -key enckey.pem -pass 1234 -out encreq.pem
        $ gmssl reqsign -in encreq.pem -days 365 -key_usage keyEncipherment -cacert cacert.pem -key cakey.pem -pass 1234 -out enccert.pem
        $ gmssl certparse -in enccert.pem

        $ cat signcert.pem > double_certs.pem
        $ cat enccert.pem >> double_certs.pem
        $ cat cacert.pem >> double_certs.pem
        ```

2. Generate the client certificate.

    ```shell
    $ gmssl sm2keygen -pass 1234 -out clientkey.pem
    $ gmssl reqgen -C CN -ST Beijing -L Haidian -O PKU -OU CS -CN Client -key clientkey.pem -pass 1234 -out clientreq.pem
    $ gmssl reqsign -in clientreq.pem -days 365 -key_usage digitalSignature -cacert cacert.pem -key cakey.pem -pass 1234 -out clientcert.pem
    $ gmssl certparse -in clientcert.pem
    ```

3. Configure the tlcpPass file.

    ```shell
    TLCP_ENC_PASS = 1234
    TLCP_SIGN_PASS = 1234
    ```

### Configuration Parameters

1. Enable the TLCP Trusted Channel switch in the database and configure the certificate paths. The paths must be absolute and no longer than 254 bytes.

    ```sql
    ALTER SYSTEM SET ssl_enable = ON SCOPE=spfile;
    ALTER SYSTEM SET ENCRYPT_TYPE = tlcp SCOPE=spfile;
    ALTER SYSTEM SET TLCP_CERT_FILE = /data/tlcp/double_certs.pem SCOPE=spfile;
    ALTER SYSTEM SET TLCP_SIGNKEY_FILE = /data/tlcp/signkey.pem SCOPE=spfile;
    ALTER SYSTEM SET TLCP_ENCKEY_FILE = /data/tlcp/enckey.pem SCOPE=spfile;
    ALTER SYSTEM SET TLCP_CACERT_FILE = /data/tlcp/cacert.pem SCOPE=spfile;
    ALTER SYSTEM SET TLCP_PASS_FILE = /data/tlcp/tlcpPass SCOPE=spfile;
    ```

    > **Warn**:
    >
    > If the TLCP Trusted Channel is enabled but the certificate path is not configured or the path is incorrect, the database will not start.

2. Restart the database.

## Client Configuration

This document provides client configuration based on the Linux platform as an example.

1. Download the server root certificate to a local path, such as /data/tlcp.

2. Create a client folder in the YashanDB client folder and create an empty file named yasc_env.ini in the client folder.

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

4. Add the following configuration in ${YASDB_HOME}/client/yasc_env.ini:

    ```ini
    TLCP_CACERT_FILE=/data/tlcp/rootcacert.pem
    TLCP_CERT_FILE=/data/tlcp/clientcert.pem
    TLCP_CLIENT_KEY_FILE=/data/tlcp/clientkey.pem
    TLCP_CLIENT_KEY_PASS = 1234
    ```

    The paths in the configuration can be specified as absolute or relative paths, but cannot contain relative paths with ../, and the system reads files according to the first 255 bytes in length.