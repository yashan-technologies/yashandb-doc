<span id="getCert" name="getCert" class="yaslink"></span>

## Generate Certificates

<span id="getSSLCert" name="getSSLCert" class="yaslink"></span>

###  SSL Related Certificates

The certificate file list for configuring SSL trusted channels ([Client\<->Server SSL Trusted Channel](./Configuring Trusted Channel.html#ssl), [Primary-Standby Replication Network Trusted Channel](./Configuring Trusted Channel.html#hassl), or [DIN Trusted Channel](./Configuring Trusted Channel.html#dinssl)) is as follows. The corresponding filenames and paths need to be recorded.

| Certificate File  | Purpose  | Filename Example |
|------------|---------------------|---------------------|
| Server Certificate | Provides server identity proof, containing the server's public key and identity information<br>Required for server configuration, corresponding to database parameter SSL_CERT_FILE | server.crt |
| Server Private Key | Used to perform core security operations such as encryption, decryption, and signing<br>Required for server configuration, corresponding to database parameter SSL_KEY_FILE | server.key |
| DH Parameter File | Diffie-Hellman parameter file, providing large prime numbers and generator parameters for forward secrecy<br>Required for server configuration, corresponding to database parameter SSL_DH_PARAM_FILE | dhparam.pem |
| CA Root Certificate | Serves as the final basis for all certificate verification, validating the validity of the entire certificate chain<br>Required for client configuration, corresponding to parameter ssl_root_cer | root.crt |

#### Preparation

- To generate SSL-related certificates, the OpenSSL tool must be used. Please refer to [Preparing Dependencies](../../../Installation and Upgrade/Installation and Deployment/Pre-Installation Preparation/Preparing Dependencies) to check and ensure that the necessary tools are installed on the server system.

- Plan and create the storage path for certificate files on one of the database servers. The YashanDB installation user (i.e., yashan) must have full permissions for the corresponding path. This chapter uses planning in the /data/yashan/ssl path as an example.

    ```shell
    $ cd /data/yashan
    $ mkdir ssl
    ```

#### Operating Steps

In the following steps, the tool command options, paths, names, etc., are unrestricted. Users can replace them with other values according to their own environment and needs.

1. Log in to the database installation server using the installation user.


2. Generate the root certificate (self-signed, includes server public key):

    ```shell
    $ cd /data/yashan/ssl
    $ openssl req -new -x509 -days 365 -nodes -out root.crt -keyout ca.key -subj "/CN=RootCA"
    ```

3. Generate the root certificate request file and server private key:

    ```shell
    $ openssl req -new -nodes -text \
    -out server.csr \
    -keyout server.key \
    -subj "/CN=server"
    ```

4. Generate and sign the server certificate:

    ```shell
    $ openssl x509 -req -in server.csr -text -days 5 \
    -CA root.crt \
    -CAkey ca.key \
    -CAcreateserial \
    -out server.crt
    ```

5. Generate the DH parameter file:

    ```shell
    $ openssl dhparam -2 -out dhparam.pem -text 2048
    ```

<span id="getTLCPCert" name="getTLCPCert" class="yaslink"></span>

###  TLCP Related Certificates

Certificate file list for configuring [Client \<-> Server TLCP Trusted Channel](./Configuring Trusted Channel.html#tlcp) is as follows. The corresponding filenames and paths need to be recorded.

| Certificate File  | Purpose  | Filename Example |
|------------|---------------------|---------------------|
| TLCP Dual Certificate Merge File | Single file containing all necessary certificates: server signing certificate, encryption certificate, and CA certificate chain, which can simplify certificate management<br>Required for server configuration, corresponding to database parameter TLCP_CERT_FILE | double_certs.pem |
| Server Signing Private Key | Used for server identity signing operations, digitally signing messages during TLS handshake, requires TLCP_SIGN_PASS from TLCP private key password file for decryption<br>Required for server configuration, corresponding to database parameter TLCP_SIGNKEY_FILE | signkey.pem |
| Server Encryption Private Key | Used for server data decryption operations, decrypting pre-master keys sent by clients during key exchange, requires TLCP_ENC_PASS from TLCP private key password file for decryption<br>Required for server configuration, corresponding to database parameter TLCP_ENCKEY_FILE | enckey.pem |
| Secondary CA Certificate | Acts as an intermediate link in the certificate chain, used to issue server and client certificates, also part of the certificate chain in TLCP dual certificate merge file<br>Required for server configuration, corresponding to database parameter TLCP_CACERT_FILE | cacert.pem |
| TLCP Private Key Password File | Stores decryption passwords for server signing and encryption private keys, providing secure password management mechanism<br>Required for server configuration, corresponding to database parameter TLCP_PASS_FILE | tlcpPass |
| CA Root Certificate | Acts as the trust root for the entire certificate chain, can issue subordinate certificates<br>Required for client configuration, corresponding to parameter TLCP_CACERT_FILE | rootcacert.pem |
| Client Signing Certificate | Client certificate in TLCP mutual authentication, serves as client identity proof<br>Required for client configuration, corresponding to parameter TLCP_CERT_FILE | clientcert.pem |
| Client Signing Private Key | Used for client identity signing operations, client certificate signature verification during TLS handshake, requires password specified during file generation for decryption<br>Required for client configuration, corresponding to parameter TLCP_CLIENT_KEY_FILE | clientkey.pem |

#### Preparation

- To generate TLCP-related certificates, the GmSSL tool must be used. Please refer to [Preparing Dependencies](../../../Installation and Upgrade/Installation and Deployment/Pre-Installation Preparation/Preparing Dependencies) to check and ensure that the required tools are installed on the server system.

- Plan and create a storage path for the certificate files on one of the database servers. The YashanDB install user (i.e., yashan) must have full privileges for the corresponding path. This chapter takes the path /data/yashan/tlcp as an example.

    ```shell
    $ cd /data/yashan
    $ mkdir tlcp
    ```

#### Operating Steps

In the following steps, the tool command options, paths, names, etc. are unrestricted, and users may replace them with other values according to their environment and needs.

1. Generate the server certificate:

    1) Generate the root certificate.

        ```shell
        $ cd /data/yashan/tlcp
        $ gmssl sm2keygen -pass 1234 -out rootcakey.pem
        $ gmssl certgen -C CN -ST Beijing -L Haidian -O PKU -OU CS -CN ROOTCA \
        -days 3650 -key rootcakey.pem -pass 1234 -out rootcacert.pem \
        -key_usage keyCertSign -key_usage cRLSign -ca
        $ gmssl certparse -in rootcacert.pem
        ```

    2) Generate the CA certificate.

        ```shell
        $ gmssl sm2keygen -pass 1234 -out cakey.pem
        $ gmssl reqgen -C CN -ST Beijing -L Haidian -O PKU -OU CS -CN "Sub CA" -key cakey.pem -pass 1234 -out careq.pem
        $ gmssl reqsign -in careq.pem -days 365 -key_usage keyCertSign \
        -path_len_constraint 0 -cacert rootcacert.pem \
        -key rootcakey.pem -pass 1234 -out cacert.pem -ca
        $ gmssl certparse -in cacert.pem
        ```

    3) Use CA certificate to sign server certificate

        ```shell
        $ gmssl sm2keygen -pass 1234 -out signkey.pem
        $ gmssl reqgen -C CN -ST Beijing -L Haidian -O PKU -OU CS -CN localhost \
        -key signkey.pem -pass 1234 -out signreq.pem
        $ gmssl reqsign -in signreq.pem -days 365 -key_usage digitalSignature -cacert cacert.pem \
        -key cakey.pem -pass 1234 -out signcert.pem
        $ gmssl certparse -in signcert.pem
        ```

    4) Use the CA certificate to issue an encryption certificate

        ```shell
        $ gmssl sm2keygen -pass 1234 -out enckey.pem
        $ gmssl reqgen -C CN -ST Beijing -L Haidian -O PKU -OU CS -CN localhost -key enckey.pem -pass 1234 -out encreq.pem
        $ gmssl reqsign -in encreq.pem -days 365 -key_usage keyEncipherment -cacert cacert.pem -key cakey.pem -pass 1234 -out enccert.pem
        $ gmssl certparse -in enccert.pem
        ```

    5) Merge the server signing certificate, server encryption certificate, and CA certificate chain to generate the TLCP dual certificate merge file.

        ```shell
        # Must strictly follow the order: signing → encryption → CA
        $ cat signcert.pem > double_certs.pem
        $ cat enccert.pem >> double_certs.pem
        $ cat cacert.pem >> double_certs.pem
        ```

2. Generate the client certificate.

    ```shell
    # Generate Client Signature Private Key File
    $ gmssl sm2keygen -pass 1234 -out clientkey.pem

    # Generate Client Certificate Signing Request File
    $ gmssl reqgen -C CN -ST Beijing -L Haidian -O PKU -OU CS -CN Client -key clientkey.pem -pass 1234 -out clientreq.pem

    # Generate and Parse Client Signature Certificate File
    $ gmssl reqsign -in clientreq.pem -days 365 -key_usage digitalSignature \
    -cacert cacert.pem -key cakey.pem \
    -pass 1234 -out clientcert.pem
    $ gmssl certparse -in clientcert.pem
    ```

3. Configure the tlcpPass file.

    ```shell
    $ vi tlcpPass
    
    TLCP_ENC_PASS = 1234    # Decryption password for server encryption private key
    TLCP_SIGN_PASS = 1234   # Decryption password for server signing private key
    ```

<span id="expire" name="expire" class="yaslink"></span>

##  Certificate Expiration Replacement

Before certificates expire, new certificate files should be generated in a timely manner.

1. [Regenerate certificates](#getCert) in the original directory and replace the old files.

2. Download the new certificate files to the certificate storage path on the client device and replace the old files.
