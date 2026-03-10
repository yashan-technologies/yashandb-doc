To improve business isolation and reduce operational errors, it is recommended to create an independent installation user for the YashanDB product. The following operations must be performed on all servers.

1. Create an installation user.

    In this installation manual, yashan is used as the database installation user name.

    ```shell
    # useradd -d /home/yashan -m yashan
    # passwd yashan
    ```

2. Create a new YASDBA user group and add the installation user to this group.

    ```shell
    # groupadd YASDBA
    # usermod -a -G YASDBA yashan
    ```

3. Configure the installation user for passwordless sudo.

    ```shell
    # cd /etc
    # ll sudoers
    ## If the root user does not have sudoers file editing privilege, it must be authorized first
    # chmod +w sudoers
    # vim /etc/sudoers

    ## Add the following content at the end of the sudoers file, save and exit
    yashan  ALL=(ALL)NOPASSWD:ALL

    ## If the privilege of the sudoers file has been adjusted, it should be restored
    # chmod -w sudoers
    ```
