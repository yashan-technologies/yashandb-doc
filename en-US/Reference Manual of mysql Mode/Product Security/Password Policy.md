
## Password Classification

User passwords in YashanDB are classified into two categories:

- Password fields stored in database tables: These are the passwords set when creating or modifying a user, and such passwords are recorded together with user information.

- Password files stored on the database server: These are the passwords set for the system user 'sys', and are independent of the database tables, allowing authentication when the database is in a non-OPEN state.



## Password Setting Scenarios

- **Initial Password for System User**

    To protect user privacy, YashanDB does not provide an initial password for the system user. Users must set it using the [yaspwd](../../Tools Guide/yaspwd) tool or the [yasboot](../../Tools Guide/yasboot/Introduction to yasboot Command/yasboot cluster) tool after product installation.

- **Password Modification for System User**

    For security reasons, it is recommended to regularly change the password for the system user. This can be done using the [yaspwd](../../Tools Guide/yaspwd) tool, [yasboot](../../Tools Guide/yasboot/Introduction to yasboot Command/yasboot cluster) tool, or the [ALTER USER](../SQL Reference/SQL Statements/ALTER USER) statement. 
    
    When modifying the password of the system user using the ALTER USER statement, the new password will be stored in both the database tables and the password file.

- **Creating Normal Users**

    When creating normal users using the [CREATE USER](../SQL Reference/SQL Statements/CREATE USER) statement, passwords can be set in plaintext or encrypted formats.



- **Password Modification for Normal Users**

    The password of a user can be modified using the ALTER USER statement.

    Normal users can actively change their own passwords or can be prompted to change them by their associated password policy.

> **Caution**: 
>
> - After changing the password, the old password becomes invalid immediately, and the user must use the new password for the next login.
>
> - When setting passwords using *yaspwd*, it is recommended to use prompts for inputting, and avoid entering the password explicitly on the command line to prevent password leakage.
>
> - When setting passwords via SQL statement, since the password must be explicitly entered, operations should be conducted in a secure environment to reduce the risk of password leakage.
