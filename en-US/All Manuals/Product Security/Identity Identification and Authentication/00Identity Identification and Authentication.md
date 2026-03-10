In YashanDB, creating a database login account means creating a [User](Managing Users) within the database. A user is the identity of the database user, associated with the user's login password, privileges, default tablespace, and information about the resources and functionalities that can be accessed/used.

Identity verification is the process of confirming the identity an entity (user) claims to have, also known as "authentication." The purpose of database authentication is to ensure that the user initiating the login request is indeed the user they claim to be. Only users who have passed authentication are allowed to enter the database and access the data within the privileges granted.

YashanDB provides the following methods of authentication:

- [Password Authentication](Password Authentication/00Password Authentication): When a user initiates a login request, they enter their username and password. YashanDB compares the provided password, which is processed through an encryption protocol (meeting GB/T 15843.2), with the salted hash of the password stored on the server for that username. Only a matching password will pass authentication. Password authentication is suitable for all users.

- [OS Authentication](OS Authentication/00OS Authentication): YashanDB recognizes the operating system's authentication of the OS user on the login server and allows direct reuse of its authentication results. An OS user can directly log into the database after authentication. Any user on the server logging into YashanDB via OS authentication will be the database super administrator sys, possessing all privileges. Therefore, OS authentication is only suitable for DBA users.

- [Two-Factor Authentication](Two-Factor Authentication/00Two-Factor Authentication): This method requires the user to provide two different authentication factors when initiating a login request, specifically a password and a UKEY, in order to reduce the risk of the database user's password being stolen. Two-factor authentication is suitable for DBA, SECURITY_ADMIN, and AUDIT_ADMIN users.

YashanDB server will only accept login requests initiated by existing database users that have successfully been authenticated.