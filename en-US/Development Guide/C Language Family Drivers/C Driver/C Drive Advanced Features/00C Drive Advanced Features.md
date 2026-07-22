The C driver, in addition to providing basic function connections and operations for databases, also possesses the following advanced functionalities:

- [Transparent Application Failover](Transparent Application Failover) (TAF) is a client-side functionality that effectively reduces the disruption of end user applications caused by database connection failures due to instance or network failures.
- [Memory Injection Management](Memory Injection Management) manages memory context through associated callback functions to support memory injection.
- [UKEY Authentication Login](UKEY Authentication Login) enables a more secure authentication login by utilizing the signing and encryption functionalities of UKEY hardware.
- [Trusted Connection](Trusted Connection) refers to downloading the server CA certificate on the application client and establishing a mutual trust channel with the database server through the certificate, ensuring the security and reliability of data transmission. This function also implements a mechanism to establish trusted connections with multiple servers simultaneously through multiple sets of certificates.

