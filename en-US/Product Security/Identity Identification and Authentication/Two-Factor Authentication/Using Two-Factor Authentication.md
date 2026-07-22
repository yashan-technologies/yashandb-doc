If dual-factor authentication is enabled for a user, the server must verify both the user's login password and the UKEY when the user logs in.

## Login using the client

1. Obtain the intelligent password key UKEY corresponding to the database user.

2. Insert the UKEY into the USB port of the device where the database client (*yasql*) is located.

3. Initiate a login request based on the username and password.

## Login using the driver

- [Login to the database with dual-factor authentication using the JDBC driver](../../../Development Guide/JDBC Driver/YashanDB JDBC Driver Usage Introduction/Connect to YashanDB/Configuring Secure Connections.md#UKEY)

- [Login to the database with dual-factor authentication using the C driver](../../../Development Guide/C Language Family Drivers/C Driver/C Drive Advanced Features/UKEY Authentication Login)