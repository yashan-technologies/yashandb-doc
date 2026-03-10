Double quotes are an extension for defining and using identifiers and passwords. When using double quotes, identifiers and password definitions will support special characters, numbers, and are case-sensitive.

The placement of double quotes (whether they can appear after SELECT, FROM, and WHERE), the rules for their usage scenarios, and the length limit of the strings within double quotes are determined by the identifiers and passwords defined by the double quotes, unrelated to whether double quotes are used.

Object name identifiers such as table names, column names, view names, sequence names, synonyms, function names, stored procedure names, trigger names, and JOB names support the use of double quotes.

Aliases for tables, columns, and views support the use of double quotes.

User passwords support any characters except for double quotes and do not support escaped double quotes (for example, "hhh^^\\"hhh").

The implementation rules for double quotes in YashanDB are as follows:

- The length of the string within double quotes is limited to 64 bytes; otherwise, an error is reported.
- Double quotes are used in pairs. Starting from the left quote, it will continue to search until the right quote is read, and the string enclosed by the double quotes will be kept without processing; an error is reported if the right quote is missing.
- DDL statements will directly write the original string (case-sensitive) within double quotes to the metadata.
- DML statements will perform a case-sensitive comparison between the original string within double quotes and the metadata string.

The following table outlines the different processing rules for whether double quotes are used in identifiers:

|Character Type |Using Double Quotes |Not Using Double Quotes |
| --- | --- | --- |
| Case Sensitivity | Perform case-sensitive comparison between the original string within double quotes and the metadata string | Convert to uppercase, then perform case-sensitive comparison with the metadata string |
| Special Characters | Compare the original string within double quotes with the metadata string | Report error |
| Numbers | Treated as name identifiers | Treated as numbers |

For the rules regarding case sensitivity and the special characters supported for identifiers as names, please refer to [Identifiers](Identifiers).