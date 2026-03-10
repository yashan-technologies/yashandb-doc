```ebnf+diagram
crypt_decrypt::= CRYPT_DECRYPT"(" expr1 "," expr2 "," expr3 "," expr4 "," expr5 ")" 
```

CRYPT_DECRYPT函数以[expr2](../通用SQL语法/expr)为算法类型、以[expr3](../通用SQL语法/expr)为算法模式、以[expr4](../通用SQL语法/expr)为HEX格式的密钥、以[expr5](../通用SQL语法/expr)为初始化随机数对[expr1](../通用SQL语法/expr)进行解密，返回解密结果。

> **Note**:
>
> 如需使用本函数且使用OpenSSL工具时，请先参照[依赖项准备](../../../安装和升级/安装部署/安装前准备/依赖项准备)检查并确保服务器系统中已安装符合要求的工具。

本函数遵循如下规则：

- 本函数不支持向量化计算。

- expr2只支持SM4。

- expr3只支持CBC。

- expr4和expr5的值均为HEX格式，长度为32个字符。

- 当expr1为null时返回null。

示例（HEAP表）

```sql
SELECT CRYPT_DECRYPT('94131D3841F5DEDA81F7651CD7C2CF63', 'sm4', 'CBC', '12345678901234567890123456789012', '12345678901234567890123456789012') AS xxxx FROM dual;

XXXX  
----- 
abc  

```
