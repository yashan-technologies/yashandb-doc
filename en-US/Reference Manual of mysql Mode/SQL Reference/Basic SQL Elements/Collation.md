Collation is the character rule declared using the 'COLLATE' keyword.

When declaring, you can specify the character sorting rule. The currently supported sorting rules for the keyword are as follows:



|Collation |Description |
|--------------------| ------------------------------------------------------------ |
| ASCII_BIN            | Compares and sorts characters as binary data.                    |
| ASCII_GENERAL_CI     | Case insensitive; treats uppercase and lowercase letters as the same character during comparison and sorting. |
| GBK_BIN              | Uses binary sorting rules.                                       |
| GBK_CHINESE_CI      | Uses Chinese language sorting rules.                             |
| UTF8MB4_BIN          | Uses binary sorting rules.                                       |
| UTF8MB4_GENERAL_CI/UTF8_GENERAL_CI   | Uses general sorting rules.                                     |
| LATIN1_BIN           | Uses binary sorting rules for the Latin alphabet character set.  |
| LATIN1_GENERAL_CI    | Uses case insensitive general sorting rules.                     |
| GB18030_BIN          | Uses binary sorting rules.                                       |
| GB18030_CHINESE_CI   | Uses pinyin sorting rules, case insensitive.                     |



The declaration method for collation literal is **[EXPR] COLLATE [COLLATION_NAME]**.

>  **Note**: 
>
>  Collation only takes effect for databases (schemas) created in mysql mode; it does not take effect for schemas created in yashan mode.

***Example*** for  Heap tables

```SQL

SQL> select true from dual where 'aa' = 'AA' collate UTF8_GENERAL_CI;

 true
-----
    1
```
