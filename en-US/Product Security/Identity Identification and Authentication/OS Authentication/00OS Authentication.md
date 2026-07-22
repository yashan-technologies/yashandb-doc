Operating system authentication means that YashanDB directly reuses the OS user authentication for login to the server, eliminating the need for database-level authentication. This allows users to connect directly to the database instance corresponding to the local YASDB_DATA environment variable without needing a YashanDB user password (or any password).

The benefits of using OS authentication include:

- Avoiding repeated password entry during usage, simplifying operations.
- Preventing the exposure of the administrator's password in some executed scripts.

Any user on the server logging into YashanDB via OS authentication will have the system user 'sys' identity in YashanDB (which means they hold the super administrator identity with all database privileges) and can perform any administrative operations.

Therefore, before allowing a user to utilize OS authentication, please ensure that the user is a system administrator of YashanDB. Additionally, it is advised that the user adheres strictly to enterprise information security regulations, such as properly safeguarding their OS password and locking their screen when leaving their seat. Otherwise, if the OS identity is compromised, the database identity may also be compromised, leading to a significant risk of data leakage and system damage.