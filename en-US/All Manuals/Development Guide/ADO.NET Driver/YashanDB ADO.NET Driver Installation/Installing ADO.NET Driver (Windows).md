## Environment Requirements

- OS version: Windows 10 and above.

- .NET version: The .NET version required by YashanDB ADO.NET driver is as follows: 
    - .NET 2.0

    - .NET 5.0

    - .NET 6.0

## Install 64-bit YashanDB ADO.NET Driver



When installing the 64-bit YashanDB ADO.NET driver, all installation packages required in the following steps must use 64-bit software packages.

### Step 1: Install .NET Environment

1. Download and install the required version of .NET through the official path.

2. Install Visual Studio or a similar integrated development environment to write and compile C# source code. This manual takes Visual Studio as an example.

3. Run `dotnet --version` to verify if the .NET environment is functioning properly:

    ```shell
    C:\>dotnet --version
    6.0.301
    ```

### Step 2: Install YashanDB C Driver

Using YashanDB ADO.NET driver requires first installing YashanDB C driver and setting environment variables. The YashanDB C driver installation files are integrated in the YashanDB client installation package, and the corresponding client installation package needs to be obtained during installation.



#### Download the C Driver Installation Package


1. From the [YashanDB Official Website Download Center](https://download.yashandb.com/download), or contact our technical support to obtain the corresponding software package. 

2. Download and extract the YashanDB client installation package to a local path, for example, /home/yasdb-driver-c/.

   After extracting the installation package, the files required for the C driver can be obtained:

   * Header files for C driver: Located in the include folder.

   * Library files for C driver: Located in the lib folder.



#### Set Environment Variables

Set the folder containing the C driver's library files to the Windows environment variable PATH. The specific operations are:

1. Right-click the "This PC" icon on the desktop and select [Properties].

2. Click on [Advanced system settings].

3. Click on [Environment Variables].

4. In the [System variables] area, select the [Path] item and click the [Edit] button below.

5. Click [New] and enter the folder where the C driver's library files are located, for example, `D:\yasdb-driver-c\lib`.

6. Click [OK] to save the configuration.






### Step 3: Install YashanDB ADO.NET Driver

1. From the [YashanDB Official Website Download Center](https://download.yashandb.com/download), or contact our technical support to obtain the YashanDB ADO.NET driver installation package.

2. Download and extract the zip file to a local path, for example, `/path/Yashandb.Data.YashandbClient`.

3. Edit the application project to reference the `Yashandb.Data.YashandbClient` project in that project. The project file example used in this manual is `Examples`. For specific content, refer to the section [YashanDB ADO.NET Driver Usage Examples](../YashanDB ADO.NET Driver Usage Examples).

    1. ) Open the application project `Examples`, click on [File > Add > Existing Project], click on the path where `Yashandb.Data.YashandbClient` is located, and select the `Yashandb.Data.YashandbClient.csproj` file to add `Yashandb.Data.YashandbClient` to the solution of the application.
    
    2. ) Right-click on the `Examples` application project, select [Add > Project Reference > Projects], and check the `Yashandb.Data.YashandbClient` option.
    

After completing the project reference, this application can access the YashanDB database.

## Install 32-bit YashanDB ADO.NET Driver

>**Note**:
>
> It is **not recommended** to install 32-bit drivers on 64-bit OS. 



When installing the 32-bit YashanDB ADO.NET driver, all installation packages required in the following steps must use 32-bit software packages.

### Step 1: Install .NET Environment

1. Download and install the required version of .NET through the official path.

2. Install Visual Studio or a similar integrated development environment to write and compile C# source code. This manual takes Visual Studio as an example.

3. Run `dotnet --version` to verify if the .NET environment is functioning properly:

    ```shell
    C:\>dotnet --version
    6.0.301
    ```

### Step 2: Install YashanDB C Driver

Using YashanDB ADO.NET driver requires first installing YashanDB C driver and setting environment variables. The YashanDB C driver installation files are integrated in the YashanDB client installation package, and the corresponding client installation package needs to be obtained during installation.



#### Download the C Driver Installation Package


1. From the [YashanDB Official Website Download Center](https://download.yashandb.com/download), or contact our technical support to obtain the corresponding software package. 

2. Download and extract the YashanDB client installation package to a local path, for example, /home/yasdb-driver-c/.

   After extracting the installation package, the files required for the C driver can be obtained:

   * Header files for C driver: Located in the include folder.

   * Library files for C driver: Located in the lib folder.



#### Set Environment Variables

Set the folder containing the C driver's library files to the Windows environment variable PATH. The specific operations are:

1. Right-click the "This PC" icon on the desktop and select [Properties].

2. Click on [Advanced system settings].

3. Click on [Environment Variables].

4. In the [System variables] area, select the [Path] item and click the [Edit] button below.

5. Click [New] and enter the folder where the C driver's library files are located, for example, `D:\yasdb-driver-c\lib`.

6. Click [OK] to save the configuration.







### Step 3: Check Dependencies

This operation is necessary only when installing the 32-bit driver.

1. Check if **32-bit** versions of ucrtbased.dll and vcruntime140d.dll are included in the `C:\Windows\SysWOW64` folder.
   
   - If not, you need to first obtain the ucrtbased.dll and vcruntime140d.dll files and save them to the `C:\Windows\SysWOW64` folder, for example, by installing Visual Studio or other methods.

   - If yes, you can directly execute subsequent operations.





### Step 4: Install YashanDB ADO.NET Driver

1. From the [YashanDB Official Website Download Center](https://download.yashandb.com/download), or contact our technical support to obtain the YashanDB ADO.NET driver installation package.

2. Download and extract the zip file to a local path, for example, `/path/Yashandb.Data.YashandbClient`.

3. Edit the application project to reference the `Yashandb.Data.YashandbClient` project in that project. The project file example used in this manual is `Examples`. For specific content, refer to the section [YashanDB ADO.NET Driver Usage Examples](../YashanDB ADO.NET Driver Usage Examples).

    1. ) Open the application project `Examples`, click on [File > Add > Existing Project], click on the path where `Yashandb.Data.YashandbClient` is located, and select the `Yashandb.Data.YashandbClient.csproj` file to add `Yashandb.Data.YashandbClient` to the solution of the application.
    
    2. ) Right-click on the `Examples` application project, select [Add > Project Reference > Projects], and check the `Yashandb.Data.YashandbClient` option.
    


4. If mixing 32-bit drivers on a 64-bit system, the project and its sub-projects (such as Yashandb.Data.YashandbClient) also need to be set to force 32-bit compilation.
    
    1. ) Right-click the application project and select [Properties].

    2. ) Open the [Build] tab, select [Platform target] as [x86], and click [Save].
