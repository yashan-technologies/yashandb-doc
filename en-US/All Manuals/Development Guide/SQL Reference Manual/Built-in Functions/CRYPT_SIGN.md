```ebnf+diagram
crypt_sign::= CRYPT_SIGN "(" expr1 "," expr2 "," expr3 "," expr4 ["," expr5] ")" 
```

The CRYPT_SIGN function uses [expr2](../General SQL Syntax/expr) as the private key to be provided by the signer, [expr3](../General SQL Syntax/expr) as the algorithm type for the signing private key, and [expr4](../General SQL Syntax/expr) as the signature ID of the signer to sign the data [expr1](../General SQL Syntax/expr), returning a signature string in HEX format of VARCHAR type.

This function follows the rules below:

- expr1 is the data to be signed and only supports VARCHAR/CHAR type.

- expr2 is the HEX format private key for signing. It can be generated using the CRYPT_KEY function to create the corresponding public-private key pair. If not provided or invalid, the function will report an error.

- expr3 is the signing algorithm type, currently only supporting SM2 type signing and verification.

- expr4 supports the signature ID in character form.

- expr5 is an optional public key parameter for the signature. Under the OpenSSL or GMSSL encryption engine, it can be omitted; however, under the cryptographic card encryption engine, it must be provided.

- If expr1 is null, the function returns null.

- The signature string returned by the function follows the ASN.1 DER encoding rules.

> **Caution**:
>
> The keys in the CRYPT_SIGN function are managed by the user. Do not write SQL containing the CRYPT_SIGN function into OUTLINE or SQLMAP views to avoid potential key leak risks.
>
> If you need to use this function and OpenSSL tools, please refer to [Dependencies Preparation](../../../Installation and Upgrade/Installation and Deployment/Pre-Installation Preparation/Preparing Dependencies) to check and ensure that the required tools are installed on the server system.
>
> Under the OpenSSL or GMSSL encryption engine, if a public key parameter is provided, ensure that the public key is valid and matches the signing private key.

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

```
