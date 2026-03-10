## Create User

Creating a user allows setting attributes such as login username, password, and privilege for the user.

- **Username**: A meaningful identifier associated with the identity, which must be globally unique and cannot be modified once created. Additionally, the username cannot be the same as the privilege name.

- **Login Password**: A password that meets security requirements must be set for the user upon creation.

- **Privilege**: Users created under the mysql mode are granted the privilege to connect to YashanDB by default, and the minimum set of privileges needed should also be granted based on actual requirements.

### Prerequisites

- The first ordinary user can only be created by the sys user.

- The user performing the user creation operation must possess CREATE USER privilege.

- The session's syntax mode must match the new user's usage requirements; that is, the user should be created in the correct mode based on what syntax the new user will use.

### Steps

1. Log into YashanDB using a user with the appropriate privilege.

2. Execute the [CREATE USER](../../../Development Guide/SQL Reference Manual/SQL Statements (mysql Mode)/CREATE USER) statement to create the user and specify the password for the new user if needed.

    ```sql
    CREATE USER SALES1 IDENTIFIED BY 'your_Password1';
    ```

### Related Operations

- Query user information: The MYSQL.USER view will present information about all users in the current database.

- Manage user privileges: New users created under the mysql mode are granted the database connection privilege by default, and [grant the corresponding additional privileges](../../Data Access Control/Privilege Management (mysql Mode)/Permission Granting and Revocation) before they can use database-related functionalities.

- Modify user attributes.

- Delete unnecessary users.

## Modify User Attributes

Modifying user attributes allows for the following operations on existing users:

- Login password related: Change password, invalidate the current password (users with expired passwords must set a new password to log in).

- Account availability related: Locking a user (locked users cannot log in), unlocking a user.

### Prerequisites

- The user performing user attribute management operations must possess CREATE USER privilege.

- The session's syntax mode must be consistent with the mode used when the user was created; that is, management must be performed in the same mode.

### Steps

1. Log into YashanDB using a user with the appropriate privilege.

2. Execute the [ALTER USER](../../../Development Guide/SQL Reference Manual/SQL Statements (mysql Mode)/ALTER USER) statement to adjust the user's attributes, including changing the password.

    ```sql
    -- Change the password of the sales user
    ALTER USER SALES1 IDENTIFIED BY 'your%Password1';

    -- Expire user password
    ALTER USER SALES2 PASSWORD EXPIRE;

    -- Lock/unlock user; after locking, the user will be unable to log in, they must be unlocked to log in
    ALTER USER SALES3 ACCOUNT LOCK;
    ```

### Related Operations

- [Manage user privileges](../../Data Access Control/Privilege and Role Management (yashan Mode)/Permission Granting and Revocation)

- Delete unnecessary users.

## Delete User

If a user is no longer needed and all their objects are also no longer needed, they can be deleted.

During the deletion process, the target user will not be able to log in, nor will they be able to create objects.

### Prerequisites

- The user performing the deletion operation must possess CREATE USER privilege.

- The session's syntax mode must match the mode used when the user was created; that is, deletion must be performed in the correct mode.

- The target user must not be logged in.

### Steps

1. Log into YashanDB using a user with the appropriate privilege.

2. Execute the DROP USER statement to delete the target user.

    ```sql
    -- Delete a user that does not have schema objects
    DROP USER SALES1;

    -- Delete a user and all their schema objects
    DROP USER SALES1 CASCADE;
    ```
