```ebnf
crypt_key = CRYPT_KEY "(" expr1 ")" .
```

The CRYPT_KEY function creates a corresponding algorithm key based on the algorithm type specified by [expr1](../General SQL Syntax/expr) and returns a key or key pair in HEX format as VARCHAR type.

The CRYPT_KEY function supports the creation of SM2 type asymmetric keys and SM4 symmetric keys.

This function follows these rules:

- expr1 only supports two algorithm types: SM2 and SM4.

- expr1 cannot be NULL or anything else; otherwise, an error will occur.

- When expr1 is 'SM2', the output public-private key pair is separated by ';', with the PUBLIC part as the public key and the PRIVATE part as the private key.

***Example***

```sql
select crypt_key('sm2') from dual;

CRYPT_KEY('SM2')                                                 
---------------------------------------------------------------- 
PUBLIC:FFBEBACC2C6A870099B325BEC9128F1CCF544B266B208A5FBEE9ECEFA17C5817A8F4C1F36E0F4CBDB3491BF584E0591F6392AB2337038B4E6DFC04F3903C2299;PRIVATE:EE4714B87EFB48FB0E58A0681E509056609D702C7C64858FF9D8C3E72A7EE65F 

1 row fetched.

select crypt_key('sm4') from dual;

CRYPT_KEY('SM4')                                                 
---------------------------------------------------------------- 
C970EF5411ADC1DE742A0044EA502B88                                

1 row fetched.
```
