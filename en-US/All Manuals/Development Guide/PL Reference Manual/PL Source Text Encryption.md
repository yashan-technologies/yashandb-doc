By default, the source code of PL objects created by users will be stored as text in the database. To prevent others from accessing such text through the data dictionary, the source code of PL objects can be encrypted.

## Encryptable Objects

The PL objects that support source code encryption include:

- Package (PACKAGE HEAD, PACKAGE BODY)

- UDF 

- Stored Procedures

For other objects, such as triggers, they can be encapsulated inside a stored procedure. Then, by encrypting the source code of that stored procedure, the source code of the trigger can be effectively encrypted.

## Encryption Tool yaswrap

[yaswrap](../../Tools Guide/yaswrap/00yaswrap) is a tool provided by YashanDB specifically for encrypting and wrapping PL source code text.

- Users can use this tool to encrypt specified code files and output the wrapped files, preventing others from viewing the PL source code through database views (e.g., *_SOURCE).

- Users can directly execute the PL objects created from the encrypted text output by *yaswrap* (the ciphertext stored in the database), and these PL objects can still function normally. Users are not aware of the system's encryption/decryption processes.

If a user needs to recreate an encrypted PL object, direct editing in the database is not allowed (as direct editing will cause the procedure body to fail execution). The source text file must be modified first, and then re-encrypted using *yaswrap*.

>**Caution**:
>
> It is not recommended to use PL source code encryption to protect/hide passwords or table names.

## Usage Example

Below is the original PL statement, stored in the mywrap.sql file:

```plsql
CREATE OR replace FUNCTION myAdd(a INT, b INT) RETURN INT AS
BEGIN 
RETURN a + b;
END;
/
```

1. Use the *yaswrap* tool to wrap mywrap.sql, generating the wrapped mywrap.plb file:

  ```shell
  $ yaswrap iname=mywrap.sql
  YashanDB PL Wrapper: Debug 22.1.0.15 x86_64

  Processing mywrap.sql to mywrap.plb
  ```

2. The generated mywrap.plb content:

  ```plsql
  CREATE OR replace FUNCTION myAdd WRAPPED
  a000000
  367
  abcd
  abcd
  abcd
  abcd
  abcd
  abcd
  abcd
  abcd
  abcd
  abcd
  abcd
  abcd
  abcd
  abcd
  abcd
  8
  45 75
  a0Q4UMeqLlU7qUUUQUA1P6sY7bCHrTewXzfgH09/NYzaR8SUHYRFD0+wHRWEIBWpoBTqEhoU
  5wJrKDYDeTm6u3r5IHFFAxUUtRKcqbxd/FypEABpYBGc

  /
  ```

3. Execute the encrypted PL statement using *yasql*:

    ```plsql
    -- Execute the wrapped PL statement
    CREATE OR replace FUNCTION myAdd WRAPPED
    a000000
    367
    abcd
    abcd
    abcd
    abcd
    abcd
    abcd
    abcd
    abcd
    abcd
    abcd
    abcd
    abcd
    abcd
    abcd
    abcd
    8
    45 75
    a0Q4UMeqLlU7qUUUQUA1P6sY7bCHrTewXzfgH09/NYzaR8SUHYRFD0+wHRWEIBWpoBTqEhoU
    5wJrKDYDeTm6u3r5IHFFAxUUtRKcqbxd/FypEABpYBGc

    /


    SELECT myAdd(1, 2) FROM dual;

        MYADD(1,2)
    ------------
                3


    -- View the function's text from USER_SOURCE

    SELECT text FROM USER_SOURCE WHERE name = 'MYADD';

    TEXT
    ----------------------------------------------------------------
    CREATE OR replace FUNCTION myAdd WRAPPED
    a000000
    354
    abcd
    abcd
    abcd
    abcd
    abcd
    abcd
    abcd
    abcd
    abcd
    abcd
    abcd
    abcd
    abcd
    abcd
    abcd
    8
    46 79
    VNCA8yPUw4/UNq3WZDDPDrXdbhkwgwzV1wzR+kdyXoU79Vmhs1rDhUfVs8xa4czsnukmbGPp
    Qsph5uB/6gknI8lB4R9JwyHMNbqo+uzVKpaV7Pumk7Jvwg==

    ```
