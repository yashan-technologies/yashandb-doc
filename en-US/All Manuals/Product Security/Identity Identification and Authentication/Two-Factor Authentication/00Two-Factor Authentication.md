Two-Factor Authentication (2FA) is a method of enhancing database user (account) security, requiring users to provide two different types of identity verification information during login. The two main types of factors typically used are:

- First Factor: Knowledge Factor, which is information that the user knows, such as a password.

- Second Factor: Possession Factor, which is something that the user possesses, such as a verification code or hardware token.

Password authentication can safeguard database security to some extent, but if the password is compromised or leaked, an attacker can easily access the database. In contrast, two-factor authentication adds a second layer of verification, making it difficult for attackers to log in even if the password is compromised, thereby increasing the difficulty of user account attacks and effectively reducing the risk of unauthorized access to the database.

Additionally, two-factor authentication can effectively prevent phishing attacks. Phishing attacks typically attempt to steal information by creating fake login pages or tricking users into providing their passwords. If two-factor authentication is enabled, even if attackers obtain the user's password, they still cannot access the database without the second factor of authentication.

In YashanDB, two-factor authentication adds a second factor of authentication based on password authentication. If two-factor authentication is enabled, users are required to provide two different authentication factors when logging into the database:

- Password: The login password corresponding to the username, which must meet the [Password Authentication](../Password Authentication/00Password Authentication) password complexity and usage policies.

- Smart Password Key USB Key (UKEY): A physical token used for identity verification, which must be initialized and export a public key before use.

Two-factor authentication is suitable for users with DBA, SECURITY_ADMIN, or AUDIT_ADMIN roles.