A user, which refers to the account logging into the database, is the identity under which the user performs specific operations in the database.

In YashanDB, users can be classified as:

- System User: This is a predefined user, serving as the super administrator responsible for database system management, possessing all privileges. The username is sys, and the password must be generated and maintained through the *yaspwd* tool.

- Regular User: This is a user created using an SQL statement, and its identity can be specified by granting authorizations to the regular user, such as DBA, security administrator, audit administrator, etc.