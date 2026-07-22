By default, the YashanDB server, Linux client, JDBC client, etc., all use the UTF8 character set, while the Windows client uses GBK.

If the character sets configured for the server and client are different, the final results of database operations may not be as expected (the strings entered on the client will be processed in the server's character set format). Please plan the character sets for the database server and clients reasonably based on actual needs.

## Configuration Principles

In YashanDB, character set-related configurations can be divided into the character set (CHARACTER_SET) and national character set (NATIONAL_CHARACTER_SET).

### Character Set

The character set configuration is controlled by the CHARACTER_SET parameter, which specifies the character encoding method for normal data types other than NCHAR, NVARCHAR, and NCLOB.

|Character Set |Configuration Principles |
|------------|-------------------------------------------------------------------------------------|
| GBK           | If the database only needs to support Chinese, with a large volume of data and high performance requirements, it is recommended to choose the double-byte fixed-length encoding Chinese character set GBK.                                     |
| UTF8          | If the application needs to handle various characters or needs to publish processing results to different language countries or regions, it is recommended to choose the Unicode character set, i.e., UTF8.<br/>This is the recommended and default character set for YashanDB. |
| ASCII         | If the database only needs to support the Latin characters included in ASCII, such as English and some Western European languages, then you can choose the ASCII character set.                                    |
| ISO88591      | This character set uses single-byte encoding, capable of representing a character range of 0-255, and is only applicable in all-English scenarios.                                                |
| GB18030       | This character set meets the implementation level three of the GB18030-2022 standard. If the database has a high volume of Chinese usage and strict requirements for the display, processing, and output of rare characters, this character set can be chosen.<br/>In YashanDB, the GB18030 character set only supports case conversion for characters within the ASCII range.         |

### National Character Set

The national character set configuration is controlled by the NATIONAL_CHARACTER_SET parameter, which specifies the character encoding method for NCHAR, NVARCHAR, and NCLOB data types, determining how to store and process this data.

|National Character Set |Configuration Principles |
|------------|-------------------------------------------------------------------------------------|
| UTF16                 | The default national character set for YashanDB, and only supports UTF16.            |

## Configuration Methods

### Setting Server Character Set

The limitations for server character sets are as follows:

- If the database has already been created, modifying the character set and national character set configuration is not allowed.

- If TAC tables or LSC tables are to be used, the server character set must be set to UTF8 or GB18030.

Methods to set the server character set:

- Initial Database: During the [installation and deployment](../../Installation and Upgrade/Installation and Deployment/00Installation and Deployment) process, the character set of the initial database can be specified by setting the CHARACTER_SET parameter in the configuration file ({cluster name}.toml).

- Non-initial Database: After installation is complete, if you need to delete the initial database and create a new one, you can specify the character set and national character set of the new database through the CHARACTER SET and NATIONAL CHARACTER SET fields in the [CREATE DATABASE](../../Development Guide/SQL Reference Manual/SQL Statements/CREATE DATABASE) statement.

   ```sql
   CREATE DATABASE yashandb CHARACTER SET utf8;
   ```

### Modifying Client Character Set

1. Create a client folder in the YashanDB client directory and an empty file yasc_env.ini in the client folder.

   ```shell
   $ mkdir client
   $ cd client
   $ vi yasc_env.ini
   ```

2. Set environment variables.

   ```shell
   $ vi ~/.bashrc
   export YASDB_HOME=/home/yasdb/yashandb_client
   $ source ~/.bashrc
   ```

   > **Note**: 
   >
   > The YASDB_HOME path needs to point to the YashanDB client folder.

3. Modify the client environment variable file yasc_env.ini to set the client character set.

   ```shell
   $ cd $YASDB_HOME/client
   $ vi yasc_env.ini
   # Add the following information, this article takes GBK as an example, actual configuration can be set to GBK, UTF8, ASCII, ISO88591 or GB18030 as needed
   CHARACTER_SET=GBK
   ```
