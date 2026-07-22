Data Masking refers to the transformation of sensitive information through masking rules to ensure that sensitive information is not disclosed during data sharing, usage, or display, achieving the concealment of sensitive data.

Data Masking converts sensitive data in the result set of a query into "fake data" that does not expose real information but has a similar structure and format. However, it does not modify the values actually stored.

YashanDB supports dynamic data masking for HEAP tables. Users can flexibly add or remove data masking strategies at any time. Each data masking strategy is independently applied to a specific column field of a table and to specific users. When a designated user performs a query on the target column, the data is masked according to the masking rules specified in the currently effective strategy. Each column can only associate with one masking strategy.

The supported masking rules are as follows:

- DEFAULT: The default masking rule, which is applied when the specified masking function is empty. The masking results for various data types are as follows:

  - Character type: Null value.

  - Numeric type: 0 (precision is not guaranteed).

  - Boolean type: FALSE.

  - Date type (except TIME type): Date part 1970-01-01, time part 00:00:00.

  - Other types: Null value.

- PARTIAL: Custom partial masking rule, which allows customization of the number of leading and trailing digits to be retained, with other digits masked by *, for example, when the parameter `partial(1,1)` is passed, `test` will be masked to `t**t`. Applicable to VARCHAR/CHAR type data, other types return null value.

- EMAIL_MASK: Masking rule specifically for email format strings, which retains only the @ symbol of the email address, replacing all other characters with *, for example, `123@sics.ac.cn` will be masked to `***@**********`. Applicable to VARCHAR/CHAR type data, other types return null value.

Adding or removing masking strategies requires calling the corresponding subprogram of the DBMS_DATAMASK advanced package. Please refer to [DBMS_DATAMASK](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_DATAMASK) for specific operations.