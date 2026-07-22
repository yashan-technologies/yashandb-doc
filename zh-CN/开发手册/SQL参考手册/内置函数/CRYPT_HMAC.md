```ebnf
crypt_hmac = CRYPT_HMAC"(" expr1 "," expr2 "," expr3 ")" .
```

CRYPT_HMAC函数以[expr3](../通用SQL语法/expr)为key、以[expr2](../通用SQL语法/expr)为算法类型对[expr1](../通用SQL语法/expr)进行HMAC计算，返回一个HEX格式的VARCHAR类型的HMAC摘要。

> **Note**:
>
> 如需使用本函数且使用OpenSSL工具时，请先参照[依赖项准备](../../../安装和升级/安装部署/安装前准备/依赖项准备.md)检查并确保服务器系统中已安装符合要求的工具。

本函数遵循如下规则：

- expr1/expr2/expr3只支持VARCHAR/CHAR类型。

- expr2仅支持SM3国密算法。

- 当expr1为null时返回null。

- expr2和expr3不能为null，若为null则函数报错。

示例

```sql
create table yashandb_hmac_test(data  varchar(255));
insert into yashandb_hmac_test values('udfghuiguighjijhihihjihjsihdjiasgdigsiajgijdsaji');
insert into yashandb_hmac_test values('fkdsjfklasdjkfjakohdfkohasoihjdfioasdhfioh');

select crypt_hmac(data, 'sm3', 'yashandb') hmac_value from yashandb_hmac_test;

select crypt_hmac(data, 'sm3', 'yashandb') hmac_value from yashandb_hmac_test;

HMAC_VALUE
---------------------------------------------------------------- 
22DEEF018DF55D9EA8E74D95D5A2C83E90927737026663B1299DA9CAE995F2A1 
EB34CE6C5C2623674D7E6ECFA95F2A8892AB97F38414A0B52F3382F259E48761 

2 rows fetched.

select crypt_hmac('hjdhsafdhasdasfdfjdn', 'sm3', 'yashandb') hmac_value from dual;

HMAC_VALUE                                                       
---------------------------------------------------------------- 
AA1DF9C2BA35F1C20CB3AB82CE144BD4B2FFC0894CEA5B054920514D76DC833B 

1 row fetched.
```
