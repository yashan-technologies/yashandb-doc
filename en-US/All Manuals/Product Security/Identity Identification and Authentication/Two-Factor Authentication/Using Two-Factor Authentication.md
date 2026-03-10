If dual-factor authentication is enabled for a user, the server must verify both the user's login password and the UKEY when the user logs in.

## Login using the client

1. Obtain the intelligent password key UKEY corresponding to the database user.

2. Insert the UKEY into the USB port of the device where the database client (*yasql*) is located.

3. Initiate a login request based on the username and password.

## Login using the driver

- [Login to the database with dual-factor authentication using the JDBC driver](../../../开发手册/JDBC驱动/JDBC驱动使用介绍/连接数据库/配置安全连接.html#UKEY)

- [Login to the database with dual-factor authentication using the C driver](../../../开发手册/C语言系驱动/C驱动/C驱动高级功能说明/UKEY认证登录)