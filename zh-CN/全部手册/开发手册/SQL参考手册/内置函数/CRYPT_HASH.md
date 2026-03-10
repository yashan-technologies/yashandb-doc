```ebnf+diagram
crypt_hash::= CRYPT_HASH"(" expr1 "," expr2 ")" 
```

CRYPT_HASH函数以[expr2](../通用SQL语法/expr)为hash算法类型，对[expr1](../通用SQL语法/expr)的数据进行哈希计算，生成相应的摘要，返回一个HEX格式的VARCHAR类型的哈希摘要。

> **Note**:
>
> 如需使用本函数且使用OpenSSL工具时，请先参照[依赖项准备](../../../安装和升级/安装部署/安装前准备/依赖项准备)检查并确保服务器系统中已安装符合要求的工具。

本函数遵循如下规则：

- expr1/expr2只支持VARCHAR/CHAR类型。

- expr2只支持SM3国密算法。

- 当expr1为null时返回null。

示例

```sql
CREATE TABLE yashandb_hash_test(data  VARCHAR(255));
INSERT INTO yashandb_hash_test VALUES('udfghuiguighjijhihihjihjsihdjiasgdigsiajgijdsaji');
INSERT INTO yashandb_hash_test VALUES('fkdsjfklasdjkfjakohdfkohasoihjdfioasdhfioh');

SELECT CRYPT_HASH(data, 'sm3') hash_value FROM yashandb_hash_test;

HASH_VALUE
---------------------------------------------------------------- 
50AD45893C2AC2413926ECEECC654C1FFE3D2BC196EEC3A19526D8388F490F30 
B9F885B217D710B3D5524B7938FC09BB269C47AB52387A7180AE13E1EC5BAD26 


SELECT CRYPT_HASH('hjdhsafdhasdasfdfjdn', 'sm3') hash_value FROM dual;

HASH_VALUE                                                       
---------------------------------------------------------------- 
DF47341FF21A5E9E395865CB26291684AB01D5CD07B38BBC0D9195B292372519 

```
