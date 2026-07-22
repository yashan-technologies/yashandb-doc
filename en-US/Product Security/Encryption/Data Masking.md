Data Masking refers to the transformation of sensitive information through masking rules to ensure that sensitive information is not disclosed during data sharing, usage, or display, achieving the concealment of sensitive data.

## Feature Introduction

YashanDB supports dynamic data masking, which desensitizes data in real - time when querying sensitive data. It transforms the sensitive data into "fake data" that does not expose real information but has a similar structure and format. This ensures the consistency and effectiveness of the actual data while masking sensitive information. For example, sensitive information such as ID numbers and contact information is stored using the actual information to ensure data validity (and functions like data transparent encryption and trusted channels can also be used to enhance data confidentiality), while when displaying information, data masking can be used to hide some digits to avoid information leakage.

Users can add or remove data masking policies for HEAP tables as needed. Each data masking policy is independently applied to a specific column field of a table and a specific user. When the specified user executes a query on the target column, the data is masked and transformed according to the masking rules specified in the latest valid policy. 

Only one data masking policy can be associated with each column.

### Data Masking Functions



The data masking rules supported by YashanDB are shown in the following table. All types of data masking rules will mask data of inapplicable data types to null values.

|Function |Rule Type  |Applicable Data Types      |Data masking Effect         |
| ------------ | -------------- | ---------------------- | --------------- |
| DEFAULT      | Default full data masking | CHAR, VARCHAR<br />TINYINT, SMALLINT, INT, BIGINT, FLOAT, DOUBLE, NUMBER, BIT<br />BOOLEAN<br />DATE, TIMESTAMP, LTZ, TZ, INTERVAL YEAR TO MONTH, INTERVAL DAY TO SECOND | * For character types: Null<br />* For numeric types: 0 (precision is not guaranteed)<br />* For boolean types: FALSE<br />* For date - time types (except TIME type): Date part 1970-01-01, time part 00:00:00 |
| EMAIL_MASK   | Email data masking | CHAR, VARCHAR | Data masking according to character set:<br />* Email strings: retain the `@` symbol and replace other parts character by character with asterisks (`*`). For example, `yashan@sics.ac.cn` will be masked to `******@**********`<br />* Non-email strings: replace each character with asterisks (`*`). For example, `yashan` will be masked to `******` |
| PREFIX_EMAIL_LOW | Email prefix data masking (low) | CHAR, VARCHAR | Data masking according to character set:<br />* Email strings: remain the first 3 characters of the prefix and replace the rest part of the prefix with 3 asterisks (`***`). If the prefix has 3 characters or fewer, retain the entire prefix and append 3 asterisks (`***`) between the prefix and the `@` symbol. For example, `yashan@sics.ac.cn` will be masked to `yas***@sics.ac.cn`, and `ya@sics.ac.cn` will be masked to `ya***@sics.ac.cn` <br />* Non-email strings: replace each character with asterisks (`*`). For example, `yashan` will be masked to `******` |
| PREFIX_EMAIL_MEDIUM | Email prefix data masking (medium) | CHAR, VARCHAR | Data masking according to character set:<br />* Email strings: replace each character of the prefix with asterisks (`*`). For example, `yashan@sics.ac.cn` will be masked to `******@sics.ac.cn`, and `ya@sics.ac.cn` will be masked to `**@sics.ac.cn` <br />* Non-email strings: replace each character with asterisks (`*`). For example, `yashan` will be masked to `******` |
| PARTIAL      | Custom partial data masking | CHAR, VARCHAR           | Based on the specified parameters, remain the first n characters and the last m characters unchanged, and replace the middle part character by character with asterisks (`*`) according to the character set. For example, if the parameter is specified as `(2,1)`, `yashan` will be desensitized to `ya***n` |
| RANDOM       | Random data masking | CHAR, VARCHAR           | Replace all characters one by one with any printable characters, and the result may vary for each query. For example, `yashan` may be desensitized to `9SV;1l` |
| REG_EXP      | Regular expression data masking | CHAR, VARCHAR           | Based on the regular expression matching the format of the target data, replace the matched content according to the character set. |
| SHUFFLE      | Shuffled data masking | CHAR, VARCHAR           | Randomly rearrange the positions of all characters within the string, and the result may be different for each query. For example, `yashan database` may be masked to `eaa aasandyshbt` |



### Functional Constraints



When using data masking in YashanDB, the following constraints must be adhered to:

- The [DBMS_DATAMASK](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_DATAMASK) package can only be called in the projection columns of a table-less query. The executing user must have the SECURITY_ADMIN role or be the sys user. The specific syntax format is `SELECT DBMS_DATAMASK.ADD_RULE(……) …… FROM DUAL`. It cannot be called through PL syntax such as the CALL statement or anonymous blocks.

- Data masking only applies to non-UDT columns and non-hidden columns of HEAP tables and cannot be used for temporary tables.

    >**Note**:
    >
    > If you correctly configure a data masking strategy for a non-UDT column in a HEAP table, then delete the table, and later recreate the table and the column with the same name but change the data type of the column with the same name to UDT, the original data masking strategy will automatically become invalid.

- Columns configured with data masking policies cannot be used for filtering or screening operations.

- It is not recommended to configure policies with data masking results that may result in null values for columns with a not-null constraint. Otherwise, errors will be returned due to null values during queries.

- There is no data masking related functionality in ISC Distributed Cluster Deployment.



## Configure Data Masking

1. Connect and log in to the database as a DBA user.
    ```shell
    $ yasql sales/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```


2. Prepare test data.

    ```sql
    CREATE TABLE customer (
        cust_id NUMBER(10) GENERATED ALWAYS AS IDENTITY 
            START WITH 1 INCREMENT BY 1 
            CONSTRAINT pk_cust_id PRIMARY KEY,
        cust_name VARCHAR(50) NOT NULL,
        phone VARCHAR(11) 
            CONSTRAINT uk_phone UNIQUE
            CONSTRAINT ck_phone CHECK (REGEXP_LIKE(phone, '^1[3-9]\d{9}$')),
        email VARCHAR(100) 
            CONSTRAINT uk_email UNIQUE
            CONSTRAINT ck_email CHECK (REGEXP_LIKE(email, '^[A-Za-z0-9._%-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,4}$')),
        CREATE_TIME DATE DEFAULT SYSDATE NOT NULL
    );
    INSERT INTO customer (cust_name, phone, email) VALUES 
    ('Yashan', '13813813800', 'yashan@example.com'),
    ('Tim', '13913913913', 'tim@example.com'),
    ('Mask', '15811111111', 'mask@example.com');

    SELECT cust_id,cust_name,phone,email FROM customer;

        CUST_ID CUST_NAME              PHONE                       EMAIL
    ----------- ---------------------- --------------------------- -------------------------------------
              1 Yashan                 13813813800                 yashan@example.com
              2 Tim                    13913913913                 tim@example.com
              3 Mask                   15811111111                 mask@example.com
    ```

3. Switch to log in as the security administrator and call DBMS_DATAMASK.ADD_RULE to add a data masking policy for the target data column.

    ```sql
    conn securitor/********

    SELECT DBMS_DATAMASK.ADD_RULE('shuffle_pol','sales.customer.cust_name',NULL,'SHUFFLE') shuffle_pol FROM dual;
    SELECT DBMS_DATAMASK.ADD_RULE('part_pol','sales.customer.phone','','PARTIAL','3,4') part_pol FROM dual;
    SELECT DBMS_DATAMASK.ADD_RULE(POL_NAME => 'email_pol',OBJ_NAME => 'sales.customer.email',FUNDESC => 'EMAIL_MASK') email_pol FROM dual;
    ```

4. Check the effect of data masking.

    ```sql
    conn sales/********
    
    SELECT cust_id,cust_name,phone,email FROM customer;
    
        CUST_ID CUST_NAME              PHONE                       EMAIL
    ----------- ---------------------- --------------------------- -------------------------------------
              1 Yaanhs                 138****3800                 ******@***********
              2 miT                    139****3913                 ***@***********
              3 Mksa                   158****1111                 ****@***********
    ```

