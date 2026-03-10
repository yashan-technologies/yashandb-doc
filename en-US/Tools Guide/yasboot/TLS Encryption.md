The *yasboot* series of tools provides TLS encryption functionality, encrypting the information transmitted between *yasboot*, yasom, and yasagent in the X.509 format, enhancing product security capabilities.

After installing YashanDB, this functionality can be enabled as needed. Before enabling, prepare the `xxx.key` private key file and the `xxx.crt` public key certificate file, where the public key certificate file can be issued by a CA or generated through self-signing.

## Generating Private Key and Certificate Files via Self-Signing

If you already have a public key, you can skip this step.

1. In any directory with read-write privileges, create a new file named `openssl.conf` and input the following content.

``` conf 
[req]
req_extensions = v3_req
distinguished_name = req_distinguished_name
prompt = no

[req_distinguished_name]
CN = yashan   # yashan is the server_name and can be named according to actual needs

[v3_req]
subjectAltName = @alt_names

[alt_names]
DNS.1 = yashan # yashan is the server_name and must be consistent with the configuration in req_distinguished_name
```

2. Execute the following command to generate the `rpc.key` private key file and the `rpc.crt` public key certificate file.

```shell
$ openssl req -new -newkey rsa:2048 -days 365 -nodes -x509 -keyout rpc.key -out rpc.crt -config openssl.conf -extensions v3_req
```

Parameter Introduction:

-  -days: The number of days the certificate is valid.
- -keyout: The name of the private key file.
- -out: The name of the public key certificate file.
- -config: The file used to generate the private and public keys.

## Enabling TLS Encryption

### Configuring the `tls.toml` File

1. Upload (or copy) the `rpc.key` private key file and the `rpc.crt` public key certificate file to each server on which YashanDB is deployed.

2. Modify the `tls.toml` file located in the **installation directory** and all **deployment directories** under `/om/cluster_name/conf/tls.toml`.

```toml
rpc_tls_enable = false              # Change to true
rpc_server_name = ""                # Change to the server_name used to generate the certificate
rpc_tls_cert_file = ""              # Change to the absolute path of rpc.crt on this server
rpc_tls_key_file = ""               # Change to the absolute path of rpc.key on this server
```

### Restarting yasom and yasagent
1. Execute the following command on each server where yasom resides to restart yasom:

``` shell
$ yasboot process yasom restart -c yashandb 
```

2. Execute the following command on all servers to restart yasagent:

``` shell
$ yasboot process yasom restart -c yashandb 
```

### Verifying Successful Activation

Execute the following command to print node information and confirm success in enabling TLS encryption based on the return result.

- Scenario 1: If the command executes without errors and successfully prints information for each database node, it indicates that TLS encryption has been successfully enabled.
- Scenario 2: If the command executes without errors but some database node information is not printed correctly, investigate whether the node is operating normally and check whether TLS encryption is correctly enabled on the server hosting the node.
- Scenario 3: If the command reports x.509 or similar errors, there may be issues with certificate configuration. Check that the paths to the `rpc.key` private key file and `rpc.crt` public key certificate file are correct in all modified `tls.toml` files.

```shell
$ yasboot cluster status -c yashandb -d
+--------------------------------------------------------------------------------------------------------------------------------------------------+
| hostid   | node_type | nodeid | pid    | instance_status | database_status | database_role | listen_address   | data_path                        |
+--------------------------------------------------------------------------------------------------------------------------------------------------+
| host0001 | db        | 1-1:1  | 114864 | open            | normal          | standby       | 192.168.1.5:1688 | /data/yashan/yasdb_data/db-1-1   |
+----------+-----------+--------+--------+-----------------+-----------------+---------------+------------------+----------------------------------+
| host0002 | db        | 1-2:2  | 114862 | open            | normal          | standby       | 192.168.1.6:1688 | /data/yashan/yasdb_data/db-1-2   |
+----------+-----------+--------+--------+-----------------+-----------------+---------------+------------------+----------------------------------+
| host0003 | db        | 1-3:3  | 6921   | open            | normal          | primary       | 192.168.1.4:1688 | /data/yashan/yasdb_data/db-1-3   |
+----------+-----------+--------+--------+-----------------+-----------------+---------------+------------------+----------------------------------+
```

## Disabling TLS Encryption

### Modifying the `tls.toml` File

Modify the `tls.toml` file located in the **installation directory** and all **deployment directories** under `/om/cluster_name/conf/tls.toml`, setting `rpc_tls_enable` to false.

```toml
rpc_tls_enable = true              # Change to false
```

### Restarting yasom and yasagent
1. Restart yasom on the server where yasom is installed:

``` shell
$ yasboot process yasom restart -c yashandb 
```

2. Restart yasagent on all servers:

``` shell
$ yasboot process yasom restart -c yashandb 
```

### Verifying Successful Deactivation

Check whether this command can print information for each database node under normal database conditions.

Execute the following command to print node information and confirm success in disabling TLS encryption based on the return result.

- Scenario 1: If the command executes without errors and successfully prints information for each database node, it indicates that TLS encryption has been successfully disabled.
- Scenario 2: If the command executes without errors but some database node information is not printed correctly, investigate whether the node is operating normally and check whether TLS encryption is correctly disabled on the server hosting the node.
- Scenario 3: If the command reports x.509 or similar errors, there may be issues with certificate configuration. Check that the paths to the `rpc.key` private key file and `rpc.crt` public key certificate file are correct in all modified `tls.toml` files.

```shell
$ yasboot cluster status -c yashandb -d
+------------------------------------------------------------------------------------------------------------------------------------------------------+
| hostid   | node_type | nodeid | pid    | instance_status | database_status | database_role | listen_address   | data_path                            |
+------------------------------------------------------------------------------------------------------------------------------------------------------+
| host0001 | db        | 1-1:1  | 114864 | open            | normal          | standby       | 192.168.1.5:1688 | /data/yashan/yasdb_data/db-1-1       |
+----------+-----------+--------+--------+-----------------+-----------------+---------------+------------------+--------------------------------------+
| host0002 | db        | 1-2:2  | 114862 | open            | normal          | standby       | 192.168.1.6:1688 | /data/yashan/yasdb_data/db-1-2       |
+----------+-----------+--------+--------+-----------------+-----------------+---------------+------------------+--------------------------------------+
| host0003 | db        | 1-3:3  | 6921   | open            | normal          | primary       | 192.168.1.4:1688 | /data/yashan/yasdb_data/db-1-3       |
+----------+-----------+--------+--------+-----------------+-----------------+---------------+------------------+--------------------------------------+
```
