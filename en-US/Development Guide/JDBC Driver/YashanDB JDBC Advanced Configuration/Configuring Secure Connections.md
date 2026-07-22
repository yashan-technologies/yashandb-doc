The JDBC secure connection configuration includes SSL encrypted communication, TLCP encrypted communication, SM3 password hash algorithm, SM4 password encryption algorithm, and UKEY secure login. These security configurations' switches are on the server side. If the server has enabled the corresponding security configuration switch, the JDBC needs to be set accordingly; otherwise, it will be unable to obtain a connection for database operations.

### SSL Encrypted Communication

If the server has enabled SSL encrypted communication, the JDBC must configure the following parameters:

|Configuration Parameter Name |Parameter Description |
|-------------------| ---------------------- |
| sslRootCer                   | Standard path to the root certificate for SSL encrypted communication, can be an absolute or relative path. |

URL connection configuration example:

```java
jdbc:yasdb://192.168.1.2:1688/yashan?sslRootCer=./root.crt 
```

### TLCP Encrypted Communication

If the server enables TLCP encrypted communication, you need to contact technical support to obtain the corresponding dynamic library from the installation package and store it in the specified path:

|Dynamic Library |Filename |Stored in the following path |
|--------------|-----| ---------------------- |
| yashandb-gmssl-jni library | * Windows: yas_gmssl_jni.dll<br/>* Linux: libyas_gmssl_jni.so | Placed in the directory included in java.library.path.<br/>Defaults include the project root directory, Path directory, and OS system directory. You can also explicitly set this path in the project startup command using the JVM parameter -Djava.library.path=XXX. |
| GmSSL library             | * Windows: gmssl.dll<br/>* Linux: libgmssl.so  | * Windows: Place in the Path.<br/>* Linux: Place in the LD_LIBRARY_PATH directory. |

In addition to the dynamic library, the JDBC must configure the following parameters:

|Configuration Parameter Name |Parameter Description |
|-------------------| ---------------------- |
| tlcpCacertFile               | Standard path to the root certificate for TLCP encrypted communication, can be an absolute or relative path. |
| tlcpCertFile                 | Standard path to the client certificate for TLCP encrypted communication, can be an absolute or relative path. |
| tlcpClientKeyFile            | Standard path to the client key file for TLCP encrypted communication, can be an absolute or relative path. |
| tlcpClientKeyPass            | Password for the client key for TLCP encrypted communication. |

URL connection configuration example:

```java
jdbc:yasdb://192.168.1.2:1688/yashan?tlcpCacertFile=D:/gmssl_ca/rootcacert.pem&tlcpCertFile=D:/gmssl_ca/clientcert.pem&tlcpClientKeyFile=D:/gmssl_ca/clientkey.pem&tlcpClientKeyPass=123456
```

### SM3 Password Hash Algorithm

If the server enables the SM3 password hash algorithm, you need to contact technical support to obtain the corresponding dynamic library from the installation package and store it in the specified path:

|Dynamic Library |Filename |Stored in the following path |
|--------------|-----| ---------------------- |
| YASJDBC dynamic library    | * Windows: YASJDBC.dll<br/>* Linux: YASJDBC.so | Placed in the directory included in java.library.path.<br/>Defaults include the project root directory, Path directory, and OS system directory. You can also explicitly set this path in the project startup command using the JVM parameter -Djava.library.path=XXX. |
| GmSSL library             | * Windows: gmssl.dll<br/>* Linux: libgmssl.so  | * Windows: Place in the Path.<br/>* Linux: Place in the LD_LIBRARY_PATH directory. |

### SM4 Password Encryption Algorithm

If the server enables the SM4 password encryption algorithm, you need to contact technical support to obtain the corresponding dynamic library from the installation package and store it in the specified path:

|Dynamic Library |Filename |Stored in the following path |
|--------------|-----| ---------------------- |
| YASJDBC dynamic library    | * Windows: YASJDBC.dll<br/>* Linux: YASJDBC.so | Placed in the directory included in java.library.path.<br/>Defaults include the project root directory, Path directory, and OS system directory. You can also explicitly set this path in the project startup command using the JVM parameter -Djava.library.path=XXX. |
| GmSSL library             | * Windows: gmssl.dll<br/>* Linux: libgmssl.so  | * Windows: Place in the Path.<br/>* Linux: Place in the LD_LIBRARY_PATH directory. |

<span id="UKEY" name="UKEY"></span>

### UKEY Secure Login
If the server enables UKEY secure login, you need to contact technical support to obtain the corresponding dynamic library from the installation package and store it in the specified path:

|Dynamic Library |Filename |Stored in the following path |
|--------------|-----| ---------------------- |
| YASJDBC dynamic library    | * Windows: YASJDBC.dll<br/>* Linux: YASJDBC.so | Placed in the directory included in java.library.path.<br/>Defaults include the project root directory, Path directory, and OS system directory. You can also explicitly set this path in the project startup command using the JVM parameter -Djava.library.path=XXX. |
| libgm3000 dynamic library | * Windows: mtoken_gm3000.dll<br/>* Linux: libgm3000.1.0.so | * Windows: Place in the Path.<br/>* Linux: Place in the LD_LIBRARY_PATH directory. |

In addition to the dynamic library, the JDBC must configure the following parameters:

|Parameter Name |Valid Values |Parameter Description |
|--------------|---------------------|------------------------------|
| UKeyName       | * DBA: If the user's role is DBA, the UKEY device name must be DBA.<br/>* SECURITY_ADMIN: If the user's role is Security Administrator, the UKEY device name must be SECURITY_ADMIN.<br/>* AUDIT_ADMIN: If the user's role is Auditor, the UKEY device name must be AUDIT_ADMIN. | This parameter indicates the UKEY device name to be authenticated, which must correspond to the user's role name. |
| UKeyPin        | Password value less than 64 bits | This parameter specifies the user password for the UKEY. |

URL connection configuration example:

```java
jdbc:yasdb://192.168.1.2:1688/yashan?UKeyName=DBA&UKeyPin=Yashan123
```