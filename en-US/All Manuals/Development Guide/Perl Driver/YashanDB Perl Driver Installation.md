## Perl Environment Preparation

To connect to YashanDB using the generic Perl application, you need to install perl, DBI, and DBD-ODBC first. The compatibility information for the dependencies required by the YashanDB Perl driver is as follows:

- Perl: 5.16.3 and above
- DBI: 1.644 and above
- DBD-ODBC: 1.61 and above

**Download and Install Perl Development Environment**

Download and install the version of perl that meets the above requirements from the official path, and configure the perl environment by yourself.

**Linux Platform Perl Environment Verification**

After configuration, verify whether the Perl environment is functioning properly by running perl --version:

```shell
# perl --version
This is perl 5, version 16, subversion 3 (v5.16.3) built for x86_64-linux-thread-multi
```

**Download and Install DBI**

Download DBI using cpan DBI, or download and compile it from the official path to install the version of DBI that meets the above requirements. Configure the DBI environment by yourself.

**Download and Install DBD-ODBC**

Download DBD::ODBC using cpan DBD::ODBC, or download and compile it from the official path to install the version of DBD::ODBC that meets the above requirements. Configure the DBD::ODBC environment by yourself.

## Install Dependencies

To use the YashanDB Perl driver, you must first install and configure the YashanDB ODBC driver library. Please refer to the installation manual [YashanDB ODBC Installation Instructions](../C Language Family Drivers/ODBC Driver/ODBC Driver Installation/00ODBC Driver Installation) section for the operation process.

## YashanDB Perl Driver Package Installation

The Perl driver does not have a driver package. Once you have completed the installation of perl, the Perl DB modules, and the YashanDB ODBC driver, you can use perl to operate on the Yashan database.