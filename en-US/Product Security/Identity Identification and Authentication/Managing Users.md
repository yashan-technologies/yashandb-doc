
A user, which refers to the account logging into the database, is the identity under which the user performs specific operations in the database.

In YashanDB, users can be classified as:

- System Users (Built-in Users):
    
    - System Administrator: When the separation of duties is not enabled, acts as the super administrator responsible for database system management, with all privileges; when the [separation of duties](../Data Access Control/Separation of Duties) is enabled, lacks audit and security management-related privileges. Username: SYS. The initial password is specified during installation, and subsequent password updates must be generated and maintained through the yaspwd tool.

    - Security Administrator: Responsible for managing database security mechanisms, such as authorizing other accounts. Username: SECURITOR. Initial password: Cod-2022. A mandatory password update is required upon first login.

    - Audit Administrator: Responsible for managing database audit mechanisms, such as creating audit policies, analyzing audit logs, and tracing user activities, including supervising DBA and Security Administrators. Username: AUDITOR. Initial password: Cod-2022. A mandatory password update is required upon first login.

- Regular User: This is a user created using an SQL statement, and its identity can be specified by granting authorizations to the regular user.


## Creating Users

Creating a user allows the administrator to set properties such as login username and password, roles/privileges, tablespace, usage quotas, and resource usage limits.

- **Username**: A meaningful identifier associated with the identity, which must be globally unique and cannot be modified once created. Additionally, the username cannot be the same as a role name or privilege name.

- **Login Password**: When creating a user, you can directly set a password that meets security requirements (the password can be used immediately), or you can set the initial password to expire (the new user must update the password upon first login).

- **Profile**: A profile can manage users' password policies, session resource limits, and login IP restrictions in bulk. It takes effect by associating the profile with target users.

- **Default Tablespace**: All objects owned by the username are stored in that user's default tablespace(s) or default temporary tablespace, unless specified otherwise when creating objects. If a default tablespace(s) is not manually set for the user, the default will be the built-in USERS tablespace(s) in YashanDB.

- **Tablespace Usage Quota**: The user's quota for a certain tablespace is divided into upper and lower limits. The upper limit indicates the maximum space allowed for the user to use that tablespace, while the lower limit indicates the space that must be reserved for the user in that tablespace. Both limits can be set independently.

- **Privileges/Roles**: After a user is successfully created, the administrator should grant the user the minimum set of privileges required, based on actual needs. It is recommended to classify and manage user privileges using roles.

### Prerequisites

- The first ordinary user can only be created by the sys user.

- The user performing the user creation operation must have the CREATE USER privilege.

- The session syntax mode must match the needs of the new user, meaning the new user should be created in the corresponding syntax mode required for their usage.

### Steps

1. Log in to YashanDB using a user with the corresponding privileges.

2. Execute the [CREATE USER](../../Development Guide/SQL Reference Manual/SQL Statements/CREATE USER) statement to create a user and specify the password, default tablespace(s), and other properties as needed.

    ```sql
    CREATE USER sales IDENTIFIED BY sales;
    CREATE USER sales1 IDENTIFIED BY password DEFAULT TABLESPACE users;

    -- To require the new user to change their password on first login, specify PASSWORD EXPIRE
    CREATE USER sales2 IDENTIFIED BY sales2 PASSWORD EXPIRE;
    ```

### Related Operations

- Query user information: The DBA_USERS view will present information about all users in the current database.

- [Manage User Privileges](../Data Access Control/Privilege and Role Management/Permission Granting and Revocation)

- Profile Management: [CREATE PROFILE](../../Development Guide/SQL Reference Manual/SQL Statements/CREATE PROFILE), [ALTER PROFILE](../../Development Guide/SQL Reference Manual/SQL Statements/ALTER PROFILE), [DROP PROFILE](../../Development Guide/SQL Reference Manual/SQL Statements/DROP PROFILE)

- Modify user properties

- Delete unnecessary users

<span id="alteruser" name="alteruser"></span>

## Modifying User Properties

Modifying user properties allows for the following operations on existing users:

- Password-related: Change password, expire current password (users with expired passwords must set a new password to log in).

- Profile-related: Associate or change profile.

- Default tablespace(s) related: Specify default tablespace(s), modify default tablespace(s).

- Tablespace usage quota-related: Set usage quota, adjust usage quota values.

- Account availability-related: Lock user (locked users cannot log in), unlock user.

### Prerequisites

- The user performing the user property management operation must have the ALTER USER privilege.

- The session syntax mode must be consistent with that at the time of user creation, meaning the management should be performed in the corresponding mode in which the user was created.

### Steps

1. Log in to YashanDB using a user with the corresponding privileges.

2. Execute the [ALTER USER](../../Development Guide/SQL Reference Manual/SQL Statements/ALTER USER) statement to adjust a user's properties, including changing the password and default tablespace(s).

    ```sql
    -- Change the password for user sales
    ALTER USER sales IDENTIFIED BY "23%ad1";

    -- Change the user's tablespace; must specify an existing tablespace
    ALTER USER sales DEFAULT TABLESPACE sales;

    -- Change the user's profile; must specify an existing profile
    ALTER USER sales PROFILE sales;

    -- Expire user password
    ALTER USER sales1 PASSWORD EXPIRE;

    -- Lock/Unlock user; locked users cannot log in and must be unlocked to log in
    ALTER USER sales1 ACCOUNT LOCK/UNLOCK;
    ```

### Related Operations

- [Manage User Privileges](../Data Access Control/Privilege and Role Management/Permission Granting and Revocation)

- Profile Management: [CREATE PROFILE](../../Development Guide/SQL Reference Manual/SQL Statements/CREATE PROFILE), [ALTER PROFILE](../../Development Guide/SQL Reference Manual/SQL Statements/ALTER PROFILE), [DROP PROFILE](../../Development Guide/SQL Reference Manual/SQL Statements/DROP PROFILE)

- Delete unnecessary users

## Deleting Users

If a user is no longer needed and all their objects are also no longer needed, the user can be deleted.

During the deletion process, the target user will not be able to log in, nor can objects be created for the target user.

### Prerequisites

- The user performing the user deletion operation must have the DROP USER privilege.

- The session syntax mode must be consistent with that at the time of user creation, meaning the deletion should be performed in the same mode in which the user was created.

- The target user must not be logged in.

### Steps

1. Log in to YashanDB using a user with the corresponding privileges.

2. Execute the [DROP USER](../../Development Guide/SQL Reference Manual/SQL Statements/DROP USER) statement to delete the target user.

    ```sql
    -- Delete a user without schema objects
    DROP USER sales1;

    -- Delete the user and all their schema objects
    DROP USER sales11 cascade;
    ```
