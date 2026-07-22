YashanDB supports LBAC, which enables strong access control at the row level. Access to data is controlled based on the labels of users and the labels of the data, allowing precise control over users' read and write privileges for each row in a table, ensuring the security of the data being read and written.

## Basic Principle



LBAC is a form of strong access control that automatically adds a column to the target table to record the label for each row when a security policy is applied — enabling data classification and grading at the row level, and assigning different labels to users to restrict their access to data. The relationship between security policies and tables allows for many-to-many mapping — once a security policy is applied to a table, it takes effect immediately.

- Label Policy: Defines the format and validation rules for labels. By applying the policy, a relationship is established between the label and the table/user.

- Label: Composed of a level and a compartment, typically formatted as `level1,level2,…:compartment1,compartment2,…`.
    - Level: Used to define the sensitivity level of protected data — the higher the value, the higher the sensitivity (e.g., 10 for "Public", 20 for "Confidential").

    - Compartment: Used to classify protected data (e.g., 1 for "Employee Information", 2 for "Customer Information").

When a user attempts to access protected data, the system compares the user's security label with the label of the target data row:

- For read access: The user is only allowed to query rows that are authorized for reading, and must satisfy the following conditions:

    - The level value in the user's maximum read label (the level value corresponding to MAX_READ_LABEL in the DBA_SA_USER_LABELS view) is greater than or equal to the level value in the label of the target data row.

    - The compartment set in the user's label includes all the compartments in the target row's label.

- For write access (INSERT, UPDATE, DELETE): The user is only allowed to modify, delete, or insert rows that are authorized for writing, and must satisfy the following conditions:

    - The level value in the user's minimum write label (the level value corresponding to MIN_WRITE_LABEL in the DBA_SA_USER_LABELS view) is greater than or equal to the level value in the label of the target data row.
    
    - The compartment set in the user's label includes all the compartments in the target row's label.



## Configuring Row Access Control

Row-level access control applies only to HEAP tables.

All LBAC-related configuration operations must be performed by users with the `LBAC_DBA` role. 

After configuring row-level access control, the following views can be used to retrieve information about LBAC label policies:

- DBA_SA_POLICIES: View all LBAC label policies in the current database.

- DBA_SA_LEVELS: View the levels within LBAC label policies.
- DBA_SA_COMPARTMENTS: View the ranges within LBAC label policies.
- DBA_SA_LABELS: View the labels within LBAC label policies.
- DBA_SA_TABLE_POLICIES: View all tables that have been configured with row-level access control, along with their corresponding policy information.
- DBA_SA_USER_LABELS: View all users who have access to controlled data, along with their corresponding policies and label information.

### Preparing Test User and Data Table

1. Connect and log in to the database as a DBA user.
    ```shell
    $ yasql sales/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```


2. Prepare a user with the `LBAC_DBA` role and two business users.

    ```sql
    CREATE USER LBACDBA IDENTIFIED BY Yr_pswd0;
    GRANT LBAC_DBA,DBA TO LBACDBA;

    CREATE USER sales1 IDENTIFIED BY Yr_pswd1;
    GRANT CONNECT,CREATE ANY TABLE,SELECT ANY TABLE,INSERT ANY TABLE TO sales1;

    CREATE USER sales2 IDENTIFIED BY Yr_pswd2;
    GRANT CONNECT,CREATE ANY TABLE,SELECT ANY TABLE,INSERT ANY TABLE,UPDATE ANY TABLE TO sales2;
    ```

4. Switch to logging in as the test user sales1 and prepare the data table.

    ```sql
    conn sales1/********

    DROP TABLE IF EXISTS product_lbac;
    CREATE TABLE product_lbac
    (product_no CHAR(5) PRIMARY KEY,
    product_name VARCHAR2(30),
    cost NUMBER,
    price NUMBER
    );
    INSERT INTO product_lbac VALUES ('10001','product001',8,10);

    SElECT product_no,product_name,cost,price FROM product_lbac;
    PRODUCT_NO PRODUCT_NAME                             COST       PRICE
    ---------- --------------------------------- ----------- -----------
    11001      product001                                  8          10
    ```

### Enabling Row Access Control

1. Switch to logging in as the user with the `LBAC_DBA` role.

    ```sql
    conn LBACDBA/********
    ```

2. Query the DBA_YLS_STATUS view to get the status of the row access control functionality.

    ```sql
    SELECT name,status FROM DBA_YLS_STATUS;
    
    NAME                                                             STATUS
    ---------------------------------------------------------------- --------- 
    YLS_ENABLE_STATUS                                                FALSE
    ```

3. If row access control has not been enabled, you must call [YLS_ENFORCEMENT](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/YLS_ENFORCEMENT).ENABLE_YLS to enable this functionality.

    ```sql
    CALL YLS_ENFORCEMENT.ENABLE_YLS;
    
    SELECT name,status FROM DBA_YLS_STATUS;
    
    NAME                                                             STATUS
    ---------------------------------------------------------------- --------- 
    YLS_ENABLE_STATUS                                                TRUE
    ```

### Applying Label Policies to Data

1. Call [SA_SYSDBA](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/SA_SYSDBA).CREATE_POLICY to create an LBAC label policy.

    ```sql
    CALL SA_SYSDBA.CREATE_POLICY ('product_lbac', 'policy_col', 'READ_CONTROL,WRITE_CONTROL');

    SELECT policy_name,column_name,status,policy_options FROM DBA_SA_POLICIES WHERE POLICY_NAME = 'PRODUCT_LBAC';
    POLICY_NAME          COLUMN_NAME            STATUS    POLICY_OPTIONS
    -------------------- ---------------------- --------- ----------------------------------------------------------------
    PRODUCT_LBAC         POLICY_COL             ENABLED   READ_CONTROL, INSERT_CONTROL, UPDATE_CONTROL, DELETE_CONTROL
    ```

2. Call [SA_COMPONENTS](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/SA_COMPONENTS).CREATE_LEVEL to create levels for the LBAC label policy.

    ```sql
    CALL SA_COMPONENTS.CREATE_LEVEL ('product_lbac', 100, 'low', 'low_level');
    CALL SA_COMPONENTS.CREATE_LEVEL ('product_lbac', 200, 'mid', 'middle_level');
    CALL SA_COMPONENTS.CREATE_LEVEL ('product_lbac', 300, 'high', 'high_level');
    CALL SA_COMPONENTS.CREATE_LEVEL ('product_lbac', 500, 'top', 'top_level');

    SELECT policy_name,level_num,short_name FROM DBA_SA_LEVELS  WHERE POLICY_NAME = 'PRODUCT_LBAC';
    POLICY_NAME                  LEVEL_NUM SHORT_NAME                                                    
    ---------------------------- --------- ---------------------
    RODUCT_LBAC                  100       LOW
    PRODUCT_LBAC                 200       MID
    PRODUCT_LBAC                 300       HIGH
    PRODUCT_LBAC                 500       TOP
    ```

3. Call [SA_COMPONENTS](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/SA_COMPONENTS).CREATE_COMPARTMENT to create compartments for the LBAC label policy.

    ```sql
    CALL SA_COMPONENTS.CREATE_COMPARTMENT ('product_lbac', 100, 'MNG', 'MANAGER');
    CALL SA_COMPONENTS.CREATE_COMPARTMENT ('product_lbac', 200, 'QA', 'QAT');
    CALL SA_COMPONENTS.CREATE_COMPARTMENT ('product_lbac', 300, 'RD', 'RDB');
    CALL SA_COMPONENTS.CREATE_COMPARTMENT ('product_lbac', 500, 'EG', 'EGA');

    SELECT policy_name,comp_num,short_name FROM DBA_SA_COMPARTMENTS WHERE POLICY_NAME = 'PRODUCT_LBAC';
    POLICY_NAME                  COMP_NUM SHORT_NAME           
    ---------------------------- -------- --------------------- 
    PRODUCT_LBAC                 100      MNG
    PRODUCT_LBAC                 200      QA
    PRODUCT_LBAC                 300      RD
    PRODUCT_LBAC                 500      EG
    ```

4. Call [SA_LABEL_ADMIN](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/SA_LABEL_ADMIN).CREATE_LABEL to create labels for the LBAC label policy.

    ```sql
    CALL SA_LABEL_ADMIN.CREATE_LABEL ('product_lbac', 100000, 'low');
    CALL SA_LABEL_ADMIN.CREATE_LABEL ('product_lbac', 200200, 'mid:QA');
    CALL SA_LABEL_ADMIN.CREATE_LABEL ('product_lbac', 300300, 'high:RD');
    CALL SA_LABEL_ADMIN.CREATE_LABEL ('product_lbac', 500500, 'top:MNG,QA,RD,EG');

    SELECT policy_name,label,label_tag,label_type FROM DBA_SA_LABELS WHERE POLICY_NAME = 'PRODUCT_LBAC';
    POLICY_NAME                  LABEL                 LABEL_TAG             LABEL_TYPE
    ---------------------------- --------------------- --------------------- -----------------
    PRODUCT_LBAC                 LOW                   100000                USER/DATA LABEL
    PRODUCT_LBAC                 MID:QA                200200                USER/DATA LABEL
    PRODUCT_LBAC                 HIGH:RD               300300                USER/DATA LABEL
    PRODUCT_LBAC                 TOP:MNG,QA,RD,EG      500500                USER/DATA LABEL
    ```

5. Call [SA_POLICY_ADMIN](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/SA_POLICY_ADMIN).APPLY_TABLE_POLICY to apply the LBAC label policy to the target data table.

    ```sql
    CALL SA_POLICY_ADMIN.APPLY_TABLE_POLICY ('product_lbac', 'sales1', 'product_lbac');

    SELECT policy_name,schema_name,table_name,status,table_options FROM DBA_SA_TABLE_POLICIES WHERE POLICY_NAME = 'PRODUCT_LBAC';
    POLICY_NAME                  SCHEMA_NAME    TABLE_NAME     STATUS    TABLE_OPTIONS                                                                                                        
    ---------------------------- -------------- -------------- --------- ----------------------------------------------------------------  
    PRODUCT_LBAC                 SALES1         PRODUCT_LBAC   ENABLED   READ_CONTROL, INSERT_CONTROL, UPDATE_CONTROL, DELETE_CONTROL

    -- View the structure of the target table
    desc sales1.product_lbac
    NAME                           NULL?     DATATYPE
    ------------------------------ --------- ---------------------------------
    PRODUCT_NO                     NOT NULL  CHAR(5)
    PRODUCT_NAME                             VARCHAR(30)
    COST                                     NUMBER
    PRICE                                    NUMBER
    POLICY_COL                               BIGINT
    ```
    From the echo information, it is known that the policy column `policy_col` has been automatically added to the target table. When inserting data into the table, you can optionally specify different label values for each row — but only label values that exist in the associated policy are allowed.

### Applying Label Policies to Users

1. Call [SA_USER_ADMIN](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/SA_USER_ADMIN).SET_USER_LABELS to apply a label policy to the target user and set the target user's labels.

    ```sql
    CALL SA_USER_ADMIN.SET_USER_LABELS ('product_lbac', 'sales1', 'high:RD');
    CALL SA_USER_ADMIN.SET_USER_LABELS ('product_lbac', 'sales2', 'mid:QA');

    SELECT user_name,policy_name,max_read_label,max_write_label,min_write_label FROM DBA_SA_USER_LABELS WHERE POLICY_NAME = 'PRODUCT_LBAC';
    USER_NAME      POLICY_NAME                  MAX_READ_LABEL        MAX_WRITE_LABEL       MIN_WRITE_LABEL
    -------------- ---------------------------- --------------------- --------------------- --------------------- 
    SALES1         PRODUCT_LBAC                 HIGH:RD               HIGH:RD               LOW               
    SALES2         PRODUCT_LBAC                 MID:QA                MID:QA                LOW
    ```

### Validating Access Control Effect

- Verify whether the access control for user sales1 has taken effect.

    1. ) Switch to logging in as the test user sales1.

        ```sql
        conn sales1/********
        ```

    2. ) View the data in the test table `product_lbac`.

        ```sql
        SElECT product_no,product_name,cost,price,policy_col FROM product_lbac;
        -- Cannot view historical data (11001, product001, 8, 10)
        ```
        When applying the policy to the table, the system will not assign label values to existing historical data rows — such data will be inaccessible to regular users (only `sys` users can read/write).
    
    3. ) Insert data into the test table `product_lbac`.

        ```sql
        -- Correct example
        INSERT INTO product_lbac VALUES ('10002','product002',13,16,100000);
        INSERT INTO product_lbac VALUES ('10003','product003',199,200,300300);

        -- Incorrect example: Specifying a non-existent label value is not allowed
        INSERT INTO product_lbac VALUES ('10004','product004',199,200,500500);
        
        -- Incorrect example: Specifying a non-existent label value is not allowed     
        INSERT INTO product_lbac VALUES ('10005','product005',1995,2200,600000);

        COMMIT;
        ```

    4. ) View the data in the test table `product_lbac` again.

        ```sql
        SElECT product_no,product_name,cost,price,policy_col FROM product_lbac;
        PRODUCT_NO PRODUCT_NAME                             COST       PRICE            POLICY_COL
        ---------- --------------------------------- ----------- ----------- ---------------------
        10002      product002                                 13          16                100000
        10003      product003                                199         200                300300
        ```

- Verify whether the access control for user sales2 has taken effect.

    1. ) Switch to logging in as the test user sales2.

        ```sql
        conn sales2/********
        ```

    2. ) View the data in the test table `product_lbac`.    

        ```sql
        SElECT product_no,product_name,cost,price,policy_col FROM sales1.product_lbac;
        PRODUCT_NO PRODUCT_NAME                             COST       PRICE            POLICY_COL
        ---------- --------------------------------- ----------- ----------- ---------------------
        10002      product002                                 13          16                100000
        ```
        The label for user sales2 is `MID:QA`, which allows them to view only rows whose level is lower than `MID` and whose compartment is a subset of `QA` (an empty set is also a subset).
    
    3. ) Perform write operations on the test table `product_lbac`.

        ```sql
        -- Correct example
        INSERT INTO sales1.product_lbac VALUES ('10007','product007',80,120,200200);
        UPDATE sales1.product_lbac SET PRICE = 25 WHERE PRODUCT_NO = 10002;

        -- Incorrect example: Updating existing data rows without read - write permissions is not allowed
        UPDATE sales1.product_lbac SET PRICE = 269 WHERE PRODUCT_NO = 10003;

        COMMIT;
        ```

    4. ) View the data in the test table `product_lbac` again.

        ```sql
        SElECT product_no,product_name,cost,price,policy_col FROM sales1.product_lbac;

        PRODUCT_NO PRODUCT_NAME                             COST       PRICE            POLICY_COL
        ---------- --------------------------------- ----------- ----------- ---------------------
        10002      product002                                 13          25                100000
        10007      product007                                 80         120                200200
        ```

    5. ) Switch to logging in as the user with the `LBAC_DBA` role, and update the label for sales2.

        ```sql
        conn LBACDBA/********

        CALL SA_USER_ADMIN.SET_USER_LABELS ('product_lbac', 'sales2', 'top:MNG,QA,RD,EG');
        ```
    
    6. ) Switch to logging in as the test user sales2, and perform read and write operations on the test table `product_lbac`.

        ```sql
        conn sales2/********

        SElECT product_no,product_name,cost,price,policy_col FROM sales1.product_lbac;
        PRODUCT_NO PRODUCT_NAME                             COST       PRICE            POLICY_COL
        ---------- --------------------------------- ----------- ----------- ---------------------
        10002      product002                                 13          16                100000
        10003      product003                                199         200                300300
        10007      product007                                 80         120                200200

        UPDATE sales1.product_lbac SET PRICE = 269 WHERE PRODUCT_NO = 10003;

        SElECT product_no,product_name,cost,price,policy_col FROM sales1.product_lbac;
        PRODUCT_NO PRODUCT_NAME                             COST       PRICE            POLICY_COL
        ---------- --------------------------------- ----------- ----------- ---------------------
        10002      product002                                 13          16                100000
        10003      product003                                199         269                300300
        10007      product007                                 80         120                200200
        ```
        After expanding the label permission scope for user sales2, the number of rows they can read and write increases.
