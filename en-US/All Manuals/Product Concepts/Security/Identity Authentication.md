Database identity verification is the process of verifying the identity claimed by a user (subject), also known as "authentication" or "identity verification." YashanDB's technical implementation of authentication includes database authentication, OS authentication, and two-factor authentication.

## Database Authentication/Password Authentication

YashanDB compares the credentials entered by the user with the stored credentials on the server, and only a completely matching password can pass the authentication.

To support user password verification at different stages of database startup, passwords can be categorized based on their storage location:

- Password File: Stored on the database server, this credential is set by the system user sys and is independent of table storage, allowing for login authentication when the database is in a non-OPEN state.

- System Table: Mainly uses the credential fields stored in the database system tables. These fields are generated based on the passwords set when creating or modifying users and are recorded along with user information.

During the password authentication process, the system will judge the user's password behavior and attributes based on the rules set by the policy and take corresponding actions, including:

- To prevent malicious attackers from repeatedly attempting to guess passwords, if a user fails to log in due to incorrect passwords continuously and reaches a threshold, they will be locked out for a certain period.

- Prolonged use of the same password may affect database security; therefore, the system imposes restrictions on the password's lifespan.

- If during the login process, the system finds that the password has been used beyond the allowed number of days, it will apply an additional grace period and log a warning message. After the grace period, the password will be fully expired, and the client will prompt the user to enter a new password, allowing the user to update the password immediately.

- To prevent users from using the same password for an extended period, users must also meet password reuse restrictions when setting a new password, which prohibits reusing passwords that were used within a certain timeframe or within the number of recent changes.

- Enable password strength control, requiring compliance with certain complexity requirements during password setting.

## OS Authentication

OS authentication means that YashanDB directly reuses the OS's identity verification for users logging into the server, eliminating the need for database-level identity verification, allowing users to connect directly to the corresponding database instance using UDS local connections without a YashanDB user password (or any password).

Any user on the server who logs into YashanDB via OS authentication will be treated as the system user sys of YashanDB (i.e., having the super administrator identity with all database privileges) and can perform any administrative operations.

## Two-Factor Authentication

Two-Factor Authentication (2FA) is a method of enhancing the security of database users (accounts) that requires the user to provide two different types of identity verification information when logging in.

In YashanDB, two-factor authentication adds a second factor of authentication based on password authentication, requiring users to provide the following when logging into the database:

- Password: The login password corresponding to the username, with complexity, usage policies, and related requirements consistent with password authentication.

- Smart Password Key USB Key (UKEY): A physical token used for identity verification.