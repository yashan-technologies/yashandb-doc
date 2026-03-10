This article takes Windows 10 Professional as an example to introduce the installation and configuration process of the YashanDB ODBC driver in this environment.

> **Note**:
> 
> The YashanDB ODBC driver relies on the C basic development library, so the application side also needs to install C compilation tools, such as gcc, Visual Studio, etc.

## Step 1: Install YashanDB C Driver



### Download the C Driver Installation Package



1. Refer to the [YashanDB Package List](../../../../安装和升级/安装部署/安装前准备/下载软件包) to obtain the YashanDB client installation package.

2. Download and extract the YashanDB client installation package to a local path, for example, /home/yasdb-driver-c/.

   After extracting the installation package, the files required for the C driver can be obtained:

   * Header files for C driver: Located in the include folder.

   * Library files for C driver: Located in the lib folder.



### Set Environment Variables

Set the folder containing the C driver's library files to the Windows environment variable PATH. The specific operations are:

1. Right-click the "This PC" icon on the desktop and select [Properties].

2. Click on [Advanced system settings].

3. Click on [Environment Variables].

4. In the [System variables] area, select the [Path] item and click the [Edit] button below.

5. Click [New] and enter the folder where the C driver's library files are located, for example, `D:\yasdb-driver-c\lib`.

6. Click [OK] to save the configuration.



## Step 2: Install YashanDB ODBC Driver

1. Obtain the YashanDB ODBC driver installation package according to the installation manual [YashanDB Software Package List](../../../../安装和升级/安装部署/安装前准备/下载软件包).

2. Download the ODBC driver installation package and unzip it to a local path, for example, `D:\yasdb-driver-odbc`.

3. Ensure that install.bat, uninstall.bat, and yas_odbc.dll are in the same path.

   ```bash
   D:\yasdb-driver-odbc>dir
   2022/11/10  10:18    <DIR>          .
   2022/11/10  10:18    <DIR>          ..
   2022/11/09  05:43             1,041 install.bat
   2022/11/09  05:43               183 README.md
   2022/11/09  05:43               181 uninstall.bat
   2022/11/09  05:43            41,984 yasodbctest.exe
   2022/11/09  05:43           144,896 yas_odbc.dll
   ```

4. Run install.bat; this operation must be run as an administrator, as follows:

   ![](./image/admin-install.png)

5. Upon successful completion, press any key to exit the installation.



## Step 3 (Optional): Check Dependencies (32-bit)

This operation is necessary only when installing the 32-bit driver.

1. Check if **32-bit** versions of ucrtbased.dll and vcruntime140d.dll are included in the `C:\Windows\SysWOW64` folder.
      
   - If not, you need to first obtain the ucrtbased.dll and vcruntime140d.dll files and save them to the `C:\Windows\SysWOW64` folder, for example, by installing Visual Studio or other methods.

   - If yes, you can directly execute subsequent operations.



<span id="datasource" name="datasource" class="yaslink"></span>

## Step 4: Configure Data Source

Use the built-in ODBC Data Source Administrator in Windows to configure the YashanDB ODBC data source.

1. Access the ODBC Data Source Administrator and add a user data source.

   - 64-bit: [Control Panel > System and Security > Administrative Tools > ODBC Data Source Administrator (64-bit)], taking 64-bit as an example.

   - 32-bit: [Control Panel > System and Security > Administrative Tools > ODBC Data Source Administrator (32-bit)]
   
   ![](./image/datasource.png)

   The YashanDB option will only appear after the YashanDB ODBC driver is successfully installed.

2. Fill in the data source information.

   ![](./image/connect.png)

   Parameters Explanation:

   - Data Source Name: The name of the ODBC data source, which uniquely identifies a data source (required).

   - Description: Data source description, equivalent to a comment (optional).

   - Server: The connection IP address of the data source, i.e., the server IP address (optional).

   - Port: The data source port, i.e., the currently listening IP port of the server (optional).

   - Url: The complete URL of the data source; please refer to the [C Driver yacConnect function](../../C驱动/C驱动接口说明/连接、授权和初始化函数/yacConnect) for the url parameter. After configuring the URL parameter, the Server and Port parameters will no longer be effective (optional).

   - User Name: The current connection user (optional).

   - PassWord: The current connection key (optional).

   Button Explanation:

   - OK: Save the current data source information; the specific save address can be seen in the registry: HKEY_CURRENT_USER\SOFTWARE\ODBC\ODBC.INI.

   - Cancel: Cancel all current modifications.

   - Test Connection: Test the current connection; the test result will pop up a dialog box.

3. Click [OK] to save the data source information. If modifications are needed later, click [Configure] to modify the current data source information.

4. Click [Test Connection], and if the connection is normal, the installation is complete.
   
   The user can now start using the YashanDB ODBC driver to develop their own client program.
