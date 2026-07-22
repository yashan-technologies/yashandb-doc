## General Description

ALTER DATABASE is used to modify the properties of a database.

This statement is first parsed according to the yashan mode syntax rules (i.e., [ALTER DATABASE](../../../Development Guide/SQL Reference Manual/SQL Statements/ALTER DATABASE.md)). If the parsing fails, it is then parsed according to the MySQL-compatible syntax rules (i.e., as described in this document). If both rules fail to match, the system returns an error.

Statement Definition
----

**alter database::=**

```ebnf
= ALTER DATABASE database_name [DEFAULT] CHARACTER SET charset_name.
```

**charset_name**


|Character Set |Configuration Principles |
|------------|-------------------------------------------------------------------------------------|
| GBK           | If the database only needs to support Chinese, with a large volume of data and high performance requirements, it is recommended to choose the double-byte fixed-length encoding Chinese character set GBK.                                     |
| UTF8          | If the application needs to handle various characters or needs to publish processing results to different language countries or regions, it is recommended to choose the Unicode character set, i.e., UTF8.<br/>This is the recommended and default character set for YashanDB. |
| ASCII         | If the database only needs to support the Latin characters included in ASCII, such as English and some Western European languages, then you can choose the ASCII character set.                                    |
| GB18030       | This character set meets the implementation level three of the GB18030-2022 standard. If the database has a high volume of Chinese usage and strict requirements for the display, processing, and output of rare characters, this character set can be chosen.<br/>In YashanDB, the GB18030 character set only supports case conversion for characters within the ASCII range.         |
| BINARY  |This character set is a binary character set that compares by byte and is case-sensitive.|
| LATIN1  |Single-byte character set that supports Western European languages, with each character occupying 1 byte.|
| UTF8MB3  |3-byte UTF-8 encoded character set that supports multilingual plane characters.|
| UTF8MB4  |4-byte UTF-8 encoded character set that supports the complete Unicode character set and emoji symbols.|


***Example*** for Standalone Deployment Heap tables

```sql
alter database sales default character set 'utf8mb4';

```
