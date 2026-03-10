HA internal node links support SSL trusted channel connections. HA node internal listening supports the following methods:

- Using client listening: Please refer to [SSL Trusted Channel Between Database Server and Client](SSL Trusted Channel Between Database Server and Client) for configuration operations.

- HA node self-listening: When using HA's own listening, the configuration method is detailed below.

## Tool Preparation

Generating certificates requires the OpenSSL tool. Please refer to [Preparing Dependencies](../../../Installation and Upgrade/Installation and Deployment/Pre-Installation Preparation/Preparing Dependencies) to check and ensure that the required tools are installed on the server system.

## Generating Certificates

The steps for certificate generation are the same as those in [SSL Trusted Channel Between Database Server and Client](SSL Trusted Channel Between Database Server and Client).

## Configuration Parameters

1. Open the HA SSL trusted channel switch in the database and configure the certificate path. The path can only be specified as an absolute path and must not exceed 254 bytes in length.

    ```sql
    ALTER SYSTEM SET ha_ssl_enable = ON SCOPE=spfile;
    ALTER SYSTEM SET ssl_cert_file = /home/yashan/YASDB_DATA/config/server.crt SCOPE=spfile;
    ALTER SYSTEM SET ssl_dh_param_file = /home/yashan/YASDB_DATA/config/dhparam.pem SCOPE=spfile;
    ALTER SYSTEM SET ssl_key_file = /home/yashan/YASDB_DATA/config/server.key SCOPE=spfile;
    ```

2. Restart the database.