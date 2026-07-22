```ebnf
crypt_sign = CRYPT_SIGN "(" expr1 "," expr2 "," expr3 "," expr4 ["," expr5] ")" .
```

CRYPT_SIGN函数以[expr2](../通用SQL语法/expr)为签名者需要提供的私钥、以[expr3](../通用SQL语法/expr)为签名私钥的算法类型、以[expr4](../通用SQL语法/expr)为签名者的签名ID对[expr1](../通用SQL语法/expr)的数据进行签名，返回一个HEX格式的VARCHAR类型的签名串。

本函数遵循如下规则：

- expr1待签名的数据只支持VARCHAR/CHAR类型。

- expr2是HEX格式的签名私钥，可以通过CRYPT_KEY函数创建相应的公私钥对，若不提供或非法，则函数报错。

- expr3是签名的算法类型，目前仅支持SM2类型的签名和验签。

- expr4支持字符形式的签名ID。

- expr5为可选的签名公钥参数，在OpenSSL或GMSSL加密引擎下，可不提供；在密码卡加密引擎下，必须提供。

- 当expr1为null时返回null。

- 函数返回的签名串，遵循ASN.1 DER编码规则。

> **Caution**:
>
> CRYPT_SIGN函数中的密钥由用户管理，请勿将包含CRYPT_SIGN函数的SQL写入OUTLINE或者SQLMAP视图中，以避免潜在的密钥泄露风险。
>
> 如需使用本函数且使用OpenSSL工具时，请先参照[依赖项准备](../../../安装和升级/安装部署/安装前准备/依赖项准备.md)检查并确保服务器系统中已安装符合要求的工具。
>
> 在OpenSSL或GMSSL加密引擎下，若提供了公钥参数，则需要保证公钥的合法性，并且与签名的私钥匹配。

示例

```sql
select crypt_key('sm2') from dual;

CRYPT_KEY('SM2')                                                 
---------------------------------------------------------------- 
PUBLIC:7CB4E274EC64D0AC2BBEC9641731E3A88BF1959572BCDB19C7A3574FC0A6C0DEF3F12B4273F1085EFA96E22415869358EA04DC49590E3464DF6DC85898E38271;PRIVATE:7048688F3345C89E90F5B1E99DED97B5901A01F1C650A99CA752088E1CE22CAA 

1 row fetched.

select crypt_sign('how are you? I am fine, thank you', '7048688F3345C89E90F5B1E99DED97B5901A01F1C650A99CA752088E1CE22CAA', 'sm2', 'lilei') signature from dual;

SIGNATURE                                                        
---------------------------------------------------------------- 
3046022100B8D8BF5A2E44575BE2B02AC0BE904867D61A605981E5AE324FF79A47C68C55DA022100B6F690EC9DBC64C68AEBF46D0A4383D99D12B516BF297E7A248AD2B9624709D7

1 row fetched.
```
