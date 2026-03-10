When YashanDB is deployed as a CDB (configuration parameter ENABLE_PLUGGABLE_DATABASE=TRUE), to meet the requirements of centralized permission control and tenant autonomy, custom-created users, roles, and profiles are divided into common and local categories.

Additionally, YashanDB has built-in the following globally available system users, roles, and user profiles.



- System users (Built-in users):
    
    - System Administrator: When the separation of duties is not enabled, acts as the super administrator responsible for database system management, with all privileges; when the [separation of duties](./Data Access Control/Separation of Duties) is enabled, lacks audit and security management-related privileges. Username: SYS. The initial password is specified during installation, and subsequent password updates must be generated and maintained through the yaspwd tool.

    - Security Administrator: Responsible for managing database security mechanisms, such as authorizing other accounts. Username: SECURITOR. Initial password: Cod-2022. A mandatory password update is required upon first login.

    - Audit Administrator: Responsible for managing database audit mechanisms, such as creating audit policies, analyzing audit logs, and tracing user activities, including supervising DBA and Security Administrators. Username: AUDITOR. Initial password: Cod-2022. A mandatory password update is required upon first login.
    
    

- Built-in roles

    The list of built-in management roles is as follows:

    |Role Name |Privilege Description |
    | -------------- | ------------------------------------------------------------ |
    | DBA            | * When the separation of duties is not enabled: Has nearly all privileges (except SHUTDOWN).<br>* When the separation of duties is enabled: Permissions are restricted — cannot perform DML operations on unprivileged database objects (tables, indexes, etc.), and cannot perform GRANT or REVOKE operations for permission management. For specific permissions, please refer to [Separation of Duties](./Data Access Control/Separation of Duties). |
    | AUDIT_ADMIN    | Has the permissions to manage database audit mechanisms, such as creating audit policies, analyzing audit logs, and so on. |
    | SECURITY_ADMIN | Has the permissions to grant privileges to other accounts, ADMINISTER KEY MANAGEMENT, and row access control.  |
    | SYSDBA         | Has the privilege to execute SHUTDOWN, backup, and BUILD (including *yasrman* and *yasbak* backup tools). |
    | SYSOPER        | Only has the privilege to execute SHUTDOWN.                       |
    | SYSBACKUP      | Only has the privileges to execute backup and BUILD (including *yasrman* and *yasbak* backup tools). |

    After granting a role from the above table to an regular user, that user immediately becomes an administrator with the corresponding management privileges included in the role. For example, a user granted the DBA role (referred to as "DBA user") is a database administrator.

    The list of built-in ordinary roles is as follows:

    |Role Name |Privilege Description |
    | -------------- | ------------------------------------------------------------ |
    | PUBLIC         | Defaults to no privileges.<br>All users have the PUBLIC role, and any privileges granted to this role will be owned by all users.<br>Please exercise caution when adjusting privileges for the PUBLIC role to avoid data leakage security risks or disrupting business operations that depend on those privileges. |
    | CONNECT        | Has CREATE SESSION privilege, allowing users with the CONNECT role to log in to a session. |
    | RESOURCE       | Has CREATE TABLE, CREATE SEQUENCE, CREATE PROCEDURE, CREATE TRIGGER, CREATE TYPE privileges. |
    | SELECT_CATALOG_ROLE | Has privileges to access V$ and GV$ views.                  |
    | AUDIT_VIEWER   | Has privileges to view system audit item views.                  |
    | LBAC_DBA       | Has privileges to perform row access control settings, allowing users with the LBAC_DBA role to execute various row access control settings. |
    | SYSKM          | Has privileges to execute wallet operations, allowing users with the SYSKM role to execute wallet-related ADMINISTER KEY MANAGEMENT statements. |

- Built-in profile

    The built-in default profile name is DEFAULT, with all parameters set to default values. All newly created users are associated with the default user profile by default when no user profile is specified, and are governed by it.

## User Classification

Custom-created users can be divided into common users and local users.

- Common users: Users that are visible to all existing containers as well as any newly created PDBs, and can be granted different permissions/roles in different PDBs. They are commonly used for unified operations, management, auditing, backup, and recovery at the entire CDB level. To create a common user, connect to the CDB root and the username must be identified by a unified prefix (default prefix is C##, which can be modified through the COMMON_USER_PREFIX parameter).

- Local users: Users that are exclusive to a specific PDB. To create a local user, connect directly to a specific PDB, and the username cannot begin with the prefix string used for common users.

## Role Classification

Custom-created roles can be divided into common roles and local roles.

- Common roles: Roles that are visible and available to all existing containers as well as any newly created PDBs. To create a common role, connect to the CDB root and the role name must be identified by a unified prefix (default prefix is C##, which can be modified through the COMMON_USER_PREFIX parameter).

    In practical applications, CDB administrators can pre-create a series of common roles with clearly defined permission sets, facilitating subsequent unified batch permission configuration and management, thereby simplifying operational processes. For example, after creating a new user in any PDB, authorization can be efficiently completed by granting a specific common role to that user, and the permission sets of all users possessing that role across all PDBs can be updated with "one click" by simply updating the permission set of the common role.

- Local roles: Roles that are exclusive to a specific PDB, facilitating independent personalized permission configuration and management for the PDB. To create a local role, connect directly to a specific PDB, and the role name cannot begin with the prefix string used for common roles.

## Profile Classification

To improve management efficiency and simplify operations, custom-created profiles can be divided into common profiles and local profiles.

- Common profiles: Profiles that are visible and available to all existing containers as well as any newly created PDBs. To create a common profile, connect to the CDB root and the profile name must be identified by a unified prefix (default prefix is C##, which can be modified through the COMMON_USER_PREFIX parameter).

    In practical applications, CDB administrators can pre-create a series of common profiles with clearly defined configurations, facilitating subsequent unified batch configuration and management of password policies, session resources, and IP black/white lists, thereby simplifying operational processes. For example, after creating a new user in any PDB, the corresponding configuration can be efficiently completed by associating the user with a specific common profile, and updating the configuration of a common profile will automatically update the corresponding configurations of all users associated with that profile across all PDBs with "one click".

- Local profiles: Profiles that are exclusive to a specific PDB, facilitating independent and personalized configuration and management. To create a local profile, connect directly to a specific PDB, and the profile name cannot begin with the prefix string used for common profiles.

##  Scenario-Based Permission Practices

Typical user and permission practice examples are as follows:

| User Identity | User Type  | Responsibilities      | Typical Permissions |
| :----------------- | :---------------------- | :---------------------------------------- | :---------------------------------- |
| SaaS Platform DBA    | Global User (c##dba)     | Responsible for the health, backup, recovery, and upgrade of the entire YashanDB system.  | Globally granted DBA, SYSBACKUP roles       |
| SaaS Platform Auditor | Global User (c##auditor)   | Audits actions of all PDBs.                | Globally granted AUDIT_VIEWER role        |
| PDB Administrator     | Local User (tenant_admin) | Manages users and business permissions within their own PDB.          | Locally granted CREATE USER, CREATE ROLE roles in target PDB |
| PDB Business User   | Local User (app_user)    | Uses SaaS applications and accesses data within their own tenant.        | Locally granted SELECT, INSERT, and other permissions on target business tables in target PDB  |

## Container Scope of Operations and Maintenance

For operations and maintenance management of common users, common roles, and common profiles, such as user/role permission management and modifying profile-related configurations, the following operational approaches can typically be selected based on operational scenarios or configuration requirements:

- Global effectiveness: Connect to the CDB root and specify container=all. In this case, the execution result will take effect on the CDB root and all PDBs.

- Local effectiveness: Connect to the target PDB and omit the container attribute. In this case, the execution result will only take effect in the executing container.

However, when performing the following operations on common users, the CDB root must be connected:

- Password updates

- Password expiration
