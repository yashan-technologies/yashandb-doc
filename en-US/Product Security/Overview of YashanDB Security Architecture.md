With the rapid development of information technology, the internet brings not only convenience but also more severe information security issues. When establishing an information security protection system, all enterprises place the database at the core of protection, making it less susceptible to hacker attacks. Additionally, the database itself is required to have strong protective capabilities to deal with external intrusion and internal information leakage, ensuring data security.

A secure database system should be able to establish a security protection mechanism and implement management measures to protect the database management system software and data from being destroyed, modified, or leaked due to accidental or malicious reasons. The overall security objectives are usually divided into requirements of confidentiality, integrity, availability, controllability, and non-repudiation:

- Confidentiality: Data must not be disclosed to unauthorized users or entities. This requirement is typically achieved through encryption, access control, and other means.

- Integrity: Data must remain unchanged, undamaged, and unlost during storage and transmission under unauthorized conditions. This requirement is generally met through access control and integrity checks.

- Availability: Data must be correctly accessible to authorized users or entities and able to recover from abnormal situations. This requirement is often achieved through access control, connection management, backup and recovery, and high availability measures.

- Controllability: The flow and behavior of information within the authorized scope can be controlled.

- Non-repudiation: Provides a basis and measures for investigating encountered information security issues.

## YashanDB Security System

In the YashanDB security system, a layered defense approach is employed, forming a multi-method, multi-level, and functionally complementary security capability framework. If one security technology or tool fails or is bypassed by an attacker, other properly configured technologies or tools can still prevent unauthorized access.

According to the idea of layered defense, we organize all security technologies in layers, realizing a security solution with multiple control layers, including data layer, audit layer, access layer, and connection layer.

|Defense Layer |Key Security Capabilities |
|--------------------|--------------------------------------|
| Data Layer                       | * Password encryption<br/>* TDE<br/>* Data dynamic desensitization<br/>* Data backup and recovery, flashback<br/>* Backup set encryption |
| Audit Layer                      | * System privilege audit<br/>* Behavior audit<br/>* Role audit |
| Access Layer                      | * Data access control: Discretionary Access Control (DAC), Role-Based Access Control (RBAC), Label-Based Access Control (LBAC)<br/>* Resource control: session resource control, persistent connections |
| Connection Layer                 | * Connection management: IP black/white list management, connection monitoring<br/>* Identity authentication: password authentication, OS authentication, two-factor authentication<br/>* Trusted channel: SSL/TLS protocol, TLCP protocol |

YashanDB provides a series of security tools, features, and strategies to meet security requirements, mainly including the following aspects:

- [Identity Identification and Authentication](Identity Identification and Authentication/00Identity Identification and Authentication): Creating an account represents creating a database user; the account represents the user's identity and includes information such as password, privileges, and default tablespace. Identity legitimacy verification of database users is completed through various authentication methods like password authentication and OS authentication. Any access that does not pass authentication will be denied by the database.

- [Data Access Control](Data Access Control/00Data Access Control): Provides fine-grained privilege control. Database users are only allowed to operate the database and access data within their privilege scope, supporting DAC, RBAC, and LBAC.

- [Security Audit](Security Audit/00Security Audit): Records user access behaviors to the database through audit mechanisms, allowing for the identification of security risks, prevention of hazardous/malicious behavior, and tracing of security issues.

- [Encryption Support](Encryption/00Encryption): Avoids the risk of data and user passwords being leaked during storage through encryption technology, ensuring the confidentiality of data.

- [Connection Management](./Connection Management/00Connection Management): Provides database anti-intrusion capabilities through connection request IP black/white lists, connection monitoring, session resource control, and other functions.

- [Data Reliability](Data Reliability/00Data Reliability): Ensures data reliability and integrity through data backup and recovery and flashback technology.

- [High Availability](High Availability/00High Availability): Ensures rapid recovery of database services in unexpected situations through real-time data redundancy technology.

- [EAL Residual Data Protection](EAL Security): Cleans up residual data after object deletion to avoid data leakage.

## Default Security Mechanism

To meet different levels of security requirements, most of the security features and strategies provided by YashanDB can be configured or have control switches. Without any adjustments, YashanDB has a set of default security mechanisms that meet the basic requirements for system operation security and data security.

|Security Item |-   |Default Mechanism |
| ------------ | -------- | ------------------------------------- |
| User         |      | No regular users; the first regular user must be created and authorized by the sys super administrator |
| Identity Authentication | Password authentication | Enabled                            |
|               | OS authentication | Enabled                            |
| Data Access Management | Separation of Duties | Disabled                            |
|               | Mandatory access control | Disabled                            |
| Security Audit |     | Disabled                                   |
| Encryption Support | Trusted channel | Disabled                            |
| Connection Management | Emergency connection resource reservation | Enabled, and cannot be disabled                              |
|               | Connection listening | Enabled                            |
|                          | Requester IP black/white list             | Disabled                                                     |
|                          | Session resource control                  | Disabled                                                     |

## User and Role System in CDB

When YashanDB is deployed as a CDB (configuration parameter ENABLE_PLUGGABLE_DATABASE=TRUE), user and role management is based on the design concept of centralized control and personalized customization, simultaneously meeting the dual requirements of global unified operations and tenant (i.e., PDB) local autonomy. For detailed introduction, please refer to [User and Role System in CDB](./User and Role System in CDB).

- Centralized control: Through super administrator privileges, unified operations, monitoring, and auditing are performed for all tenants. This capability is achieved through global user and global role mechanisms, ensuring unified control and security protection for the entire multi-tenant environment.

- Tenant autonomy: Each tenant can independently manage its own user system and permission allocation, ensuring security isolation and autonomous management between tenants. This requirement is fulfilled through local user and local role mechanisms, guaranteeing the independent operational capabilities of each tenant.
