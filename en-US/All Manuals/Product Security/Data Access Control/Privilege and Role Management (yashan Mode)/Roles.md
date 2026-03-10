The role (Role) itself has no meaning of privilege, but when a set of privileges is assigned to a role, the role can participate in the privilege system and greatly simplify the management of privileges. For example, after granting a role to a user, the user will have all the privileges currently or in the future possessed by that role, and the user's permissible operations on the database will change as the role changes.

When managing privileges based on roles, administrators only need to focus on the user's role, and on the role's privilege set. For example:

- A role can be defined to categorize a class of users' actions. Users' actions may be determined by their positions and functions, and users in the same position may have similar privilege needs.

- By granting one or more specific roles to a user, all privileges of that user can be controlled, simplifying the authorization process.

- In some cases, by revoking a user's role or a certain privilege from the role, the privilege range can be quickly narrowed down in bulk.

<span id="buildinroles" name="buildinroles" class="yaslink"></span>

## Built-in Roles

YashanDB has predefined roles with different privileges to facilitate the definition of administrators/users with different responsibilities.

The list of built-in management roles is as follows:

|Role Name |Privilege Description |
| -------------- | ------------------------------------------------------------ |
| DBA            | * When the separation of duties is not enabled: Has nearly all privileges (except SHUTDOWN).<br>* When the separation of duties is enabled: Permissions are restricted — cannot perform DML operations on unprivileged database objects (tables, indexes, etc.), and cannot perform GRANT or REVOKE operations for permission management.  |
| AUDIT_ADMIN    | Has the permissions to manage database audit mechanisms, such as creating audit policies, analyzing audit logs, and so on. |
| SECURITY_ADMIN | Has the permissions to grant privileges to other accounts, ADMINISTER KEY MANAGEMENT, and row access control.  |
| SYSDBA         | Has the privilege to execute SHUTDOWN, backup, and BUILD (including *yasrman* and *yasbak* backup tools). |
| SYSOPER        | Only has the privilege to execute SHUTDOWN.                       |
| SYSBACKUP      | Only has the privileges to execute backup and BUILD (including *yasrman* and *yasbak* backup tools). |

After granting a role from the above table to an ordinary user, that user immediately becomes an administrator with the corresponding management privileges included in the role. For example, a user granted the DBA role (referred to as "DBA user") is a database administrator.

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

## Custom Roles

Users can design corresponding roles based on their own privilege mechanisms, and each role must include the following elements:

- **Role Name**: A meaningful identifier associated with the privilege set contained in the role, required to be globally unique and cannot be modified once created. Additionally, the role name cannot be the same as a username or privilege name.

- **Privilege Set**: Grant ( [GRANT](../../../Development Guide/SQL Reference Manual/SQL Statements (yashan Mode)/GRANT)) a certain privilege/role to the target role.

> **Note**:
>
> In an ISC Distributed Cluster Deployment, roles cannot be granted to another role.

### Creating Custom Roles

#### Prerequisites

- The user performing the role creation operation must have the CREATE ROLE privilege. For authorization operations, please refer to [Permission Granting and Revocation](Permission Granting and Revocation).

- The user executing the role privilege set configuration must have the corresponding GRANT privilege (GRANT ANY PRIVILEGE, GRANT ANY OBJECT PRIVILEGE, or GRANT ANY ROLE) for the relevant privilege/role.

#### Steps

1. Log in to YashanDB with a user that has the appropriate privileges.

2. Execute the [CREATE ROLE](../../../Development Guide/SQL Reference Manual/SQL Statements (yashan Mode)/CREATE ROLE) statement to create a custom role.

    ```sql
    CREATE ROLE reader;
    ```

3. Execute the [GRANT](../../../Development Guide/SQL Reference Manual/SQL Statements (yashan Mode)/GRANT) statement to grant the target privilege to the newly created role.

    ```sql
    GRANT READ ANY TABLE TO reader;
    ```

### Deleting Custom Roles

If a custom role is no longer needed, it can be deleted.

Once a custom role is deleted, the relationship between the role and privileges will cease to exist, and all users granted that role will also lose the corresponding privileges (there may be a delay).

When deleting a custom role, all authorized records of that role will also be removed.

> **Caution**: 
>
> Please delete custom roles cautiously, as inadvertent operations may cause business operations to fail due to insufficient privileges.

#### Prerequisites

The user executing the role deletion operation must have the DROP ANY ROLE privilege. For authorization operations, please refer to [Permission Granting and Revocation](Permission Granting and Revocation).

#### Steps

1. Log in to YashanDB with a user that has the appropriate privileges.

2. Execute the [DROP ROLE](../../../Development Guide/SQL Reference Manual/SQL Statements (yashan Mode)/DROP ROLE) statement to delete a specific custom role.

    ```sql
    DROP ROLE reader;
    ```
