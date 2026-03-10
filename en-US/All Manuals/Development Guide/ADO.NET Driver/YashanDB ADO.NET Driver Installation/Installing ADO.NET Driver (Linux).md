## Environment Requirements

The .NET version required by YashanDB ADO.NET driver is as follows: 

- .NET 2.0

- .NET 5.0

- .NET 6.0

## Install .NET Environment

1. Download and install the above versions of .NET for Linux from the official path.

2. Run `dotnet --version` to verify if the .NET environment is functioning properly:

    ```shell
    $ dotnet --version
    6.0.301
    $ 
    ```

## Install YashanDB C Driver

Using YashanDB ADO.NET driver requires first installing YashanDB C driver and setting environment variables. The YashanDB C driver installation files are integrated in the YashanDB client installation package, and the corresponding client installation package needs to be obtained during installation.



### Step 1: Download the C Driver Installation Package

1. From the [YashanDB Official Website Download Center](https://download.yashandb.com/download) or contact our technical support to obtain the corresponding software package.

2. Download and extract the YashanDB client installation package to a local path, for example, /home/yasdb-driver-c/.

   After extracting the installation package, the files required for the C driver can be obtained:

   * bin: Executable files for the C driver (currently includes *yasql*).

   * Header files for C driver: Located in the include folder.

   * Library files for C driver: Located in the lib folder.



## Install YashanDB ADO.NET Driver

1. From the [YashanDB Official Website Download Center](https://download.yashandb.com/download) or contact our technical support to obtain the corresponding software package.

2. Download and extract the zip file to a local path, for example, `/path/Yashandb.Data.YashandbClient`.

3. Navigate to the folder where the application project is located, and use the dotnet command to add the `Yashandb.Data.YashandbClient` project to the application project. The project file example used in this manual is `Examples`. For specific content, refer to the section [YashanDB ADO.NET Driver Usage Examples](../ADO.NET驱动使用示例).

   ```bash
   dotnet add reference /path/Yashandb.Data.YashandbClient/Yashandb.Data.YashandbClient.csproj
   ```
    
After completing the project reference, this application can access the YashanDB database.
