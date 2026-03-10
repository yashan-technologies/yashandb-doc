```ebnf+diagram
crypt_verify::= CRYPT_VERIFY "(" expr1 "," expr2 "," expr3 "," expr4 "," expr5 ")" 
```

The CRYPT_VERIFY function verifies the data from [expr1](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr) against the signature from [expr2](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr) using the public key provided in [expr3](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr), the signature public key algorithm type in [expr4](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr), and the signer's signature ID in [expr5](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr), returning a verification result of TRUE or FALSE.

A return result of TRUE indicates that the verification passed, while FALSE indicates that the verification failed.

> **Note**:
>
> If you need to use this function and use the OpenSSL tool, please first refer to [Dependency Preparation](../../All Manuals/Installation and Upgrade/Installation and Deployment/Pre-Installation Preparation/Preparing Dependencies) to check and ensure that the required tools are installed on the server system.

This function follows these rules:

- expr1 data supports only VARCHAR/CHAR types.

- expr2 is the output of the CRYPT_SIGN function.

- expr3 is the public key in HEX format, which can be created with the CRYPT_KEY function to generate the corresponding public and private key pair. If not specified, the function will error.

- expr4 is the algorithm type for signature verification, currently only supporting SM2 type signatures and verifications.

- If either expr1 or expr2 is null, the verification fails and returns FALSE.

***Example***

```sql
SELECT CRYPT_KEY('sm2') FROM dual;

CRYPT_KEY('SM2')                                                 
---------------------------------------------------------------- 
PUBLIC:7CB4E274EC64D0AC2BBEC9641731E3A88BF1959572BCDB19C7A3574FC0A6C0DEF3F12B4273F1085EFA96E22415869358EA04DC49590E3464DF6DC85898E38271;PRIVATE:7048688F3345C89E90F5B1E99DED97B5901A01F1C650A99CA752088E1CE22CAA 


SELECT CRYPT_SIGN('how are you? I am fine, thank you', '7048688F3345C89E90F5B1E99DED97B5901A01F1C650A99CA752088E1CE22CAA', 'sm2', 'lilei') signature FROM dual;

SIGNATURE                                                        
---------------------------------------------------------------- 
3046022100B8D8BF5A2E44575BE2B02AC0BE904867D61A605981E5AE324FF79A47C68C55DA022100B6F690EC9DBC64C68AEBF46D0A4383D99D12B516BF297E7A248AD2B9624709D7

    
SELECT CRYPT_VERIFY('how are you? I am fine, thank you', '3046022100B8D8BF5A2E44575BE2B02AC0BE904867D61A605981E5AE324FF79A47C68C55DA022100B6F690EC9DBC64C68AEBF46D0A4383D99D12B516BF297E7A248AD2B9624709D7', '7CB4E274EC64D0AC2BBEC9641731E3A88BF1959572BCDB19C7A3574FC0A6C0DEF3F12B4273F1085EFA96E22415869358EA04DC49590E3464DF6DC85898E38271', 'sm2', 'lilei') verify_result FROM dual;

VERIFY_RESULT 
------------- 
true         


SELECT CRYPT_VERIFY('how are you? I am fine, thank you', '3046022100B8D8BF5A2E44575BE2B02AC0BE904867D61A605981E5AE324FF79A47C68C55DA022100B6F690EC9DBC64C68AEBF46D0A4383D99D12B516BF297E7A248AD2B9624709D7', '7CB4E274EC64D0AC2BBEC9641731E3A88BF1959572BCDB19C7A3574FC0A6C0DEF3F12B4273F1085EFA96E22415869358EA04DC49590E3464DF6DC85898E38271', 'sm2', 'hanmeimei') verify_result FROM dual;

VERIFY_RESULT 
------------- 
false        

```
