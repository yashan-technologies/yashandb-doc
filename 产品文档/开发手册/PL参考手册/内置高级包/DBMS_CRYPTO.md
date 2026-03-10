DBMS_CRYPTO高级包提供了一组内置的函数，用于数据进行加密和解密。

DBMS_CRYPTO高级包可以对常见的数据类型数据进行加解密，包括RAW类型以及能隐式转换为RAW类型的其他类型。

DBMS_CRYPTO预定义了如下加密算法、分组密码链接模式和密码块填充模式以及加密哈希算法：

| 分类     | 算法/模式         | 说明                                 |
|---------|-----------|--------------------------------------|
| 加密算法 | DBMS_CRYPTO.ENCRYPT_AES128 | 使用128位密钥，高级加密标准 |
|  | DBMS_CRYPTO.ENCRYPT_DES | 使用56位的密钥 |
|  | DBMS_CRYPTO.ENCRYPT_3DES | 在同一个块上加密3次 |
|  | DBMS_CRYPTO.ENCRYPT_3DES_2KEY | 在同一个块上使用2个密钥加密3次，使用112位的密钥 |
| 分组密码链接模式 | DBMS_CRYPTO.CHAIN_CBC | 执行流程如下：<br/>1. 将明文切分成若干小段<br/>2. 将每个小段与初始块或上一段密文进行异或运算<br/>3. 与密钥进行加密 |
| 密码块填充模式 | DBMS_CRYPTO.PAD_NONE | 无填充，需确保src必须为分组加密的块大小的整数倍、key必须为分组加密的块大小，否则会返回错误 |
|  | DBMS_CRYPTO.PAD_PKCS7 | 公钥加密标准#7（PKCS#7）填充|
| 加密哈希算法 | DBMS_CRYPTO.HASH_SH256 | SHA-2系列，生成256位散列值|
|  | DBMS_CRYPTO.HASH_MD5 | 生成128位散列值|

## DECRYPT

```plsql
DBMS_CRYPTO.DECRYPT(
   src IN RAW,
   typ IN INTEGER,
   key IN RAW,
   iv  IN RAW DEFAULT NULL)
 RETURN RAW;
```

DECRYPT函数用于对数据进行解密。

| 参数     | 描述                                                        |
| -------- | ------------------------------------------------------------ |
|src|待解密的密文数据，必须为RAW类型或能隐式转换为RAW类型的其他类型，且不能为NULL。|
|typ|使用的加解密算法及其属性配置，必须为INTEGER类型。|
|key|用于解密的密钥，必须为RAW类型或能隐式转换为RAW类型的其他类型，且不能为NULL。|
|iv |可选参数，块密码的初始化向量，必须为RAW类型或能隐式转换为RAW类型的其他类型，默认为NULL。|

本函数遵循如下规则：

- src允许为空，为空时函数返回空。

- typ不能为空，需通过使用包中的常量进行指定，且必须指定加解密算法、分组密码链接模式和密码块填充模式。
    
    当密码块填充模式为DBMS_CRYPTO.PAD_NONE时，需确保src为分组加密的块大小的整数倍，key为分组加密的块大小。

示例

```sql
DECLARE
    l_input RAW(100) := HEXTORAW('97ED8C1EE5846E622BEB372600EEA263');
    l_key RAW(30) := HEXTORAW('01234567899876543210012345678912');
    l_decrypt RAW(2000);
BEGIN 
    l_decrypt := DBMS_CRYPTO.DECRYPT(
                    src => l_input,
                    typ => DBMS_CRYPTO.ENCRYPT_AES128 + DBMS_CRYPTO.CHAIN_CBC + DBMS_CRYPTO.PAD_NONE,
                    key  => l_key,
                    iv => NULL
                );

    DBMS_OUTPUT.PUT_LINE('decrypt value: ' || l_decrypt);
END;
/

-- result
decrypt value: 01234567899876543210012345678912

DECLARE
    l_input RAW(100) := HEXTORAW('BE924F9786C4A65D65F63A99C03D7401');
    l_key RAW(30) := HEXTORAW('30313233343536373839414243444546');
    l_decrypt RAW(2000);
BEGIN 
    l_decrypt := DBMS_CRYPTO.DECRYPT(
                    src => l_input,
                    typ => DBMS_CRYPTO.ENCRYPT_AES128 + DBMS_CRYPTO.CHAIN_CBC + DBMS_CRYPTO.PAD_PKCS7,
                    key  => l_key,
                    iv => NULL
                );

    DBMS_OUTPUT.PUT_LINE('decrypt value: ' || l_decrypt);
END;
/

-- result
decrypt value: 596F757253656372657444617461
```

## ENCRYPT

```plsql
DBMS_CRYPTO.ENCRYPT(
   src IN RAW,
   typ IN INTEGER,
   key IN RAW,
   iv  IN RAW   DEFAULT NULL)
 RETURN RAW;
```

ENCRYPT函数用于对数据进行加密。

| 参数     | 描述                                                        |
| -------- | ------------------------------------------------------------ |
|src|待加密的源数据，必须为RAW类型或能隐式转换为RAW类型的其他类型，且不能为NULL。|
|typ|使用的加密算法及其属性配置，必须为INTEGER类型。|
|key|用于加密的密钥，必须为RAW类型或能隐式转换为RAW类型的其他类型，且不能为NULL。|
|iv |可选参数，块密码的初始化向量，必须为RAW类型或能隐式转换为RAW类型的其他类型，默认为NULL。|

本函数遵循如下规则：

- src允许为空，src为空时函数返回空。

- typ不能为空，需通过使用包中的常量进行指定，且必须指定加密算法、分组密码链接模式和密码块填充模式。

    当密码块填充模式为DBMS_CRYPTO.PAD_NONE时，需确保src为分组加密的块大小的整数倍，key为分组加密的块大小。

示例（HEAP表）

```sql
DECLARE
    l_input RAW(100) := HEXTORAW('01234567899876543210012345678912');
    l_key RAW(16) := HEXTORAW('01234567899876543210012345678912');
    l_encrypt RAW(2000);
BEGIN 
    l_encrypt := DBMS_CRYPTO.ENCRYPT(
                    src => l_input,
                    typ => DBMS_CRYPTO.ENCRYPT_AES128 + DBMS_CRYPTO.CHAIN_CBC + DBMS_CRYPTO.PAD_NONE,
                    key  => l_key,
                    iv => NULL
                );

    DBMS_OUTPUT.PUT_LINE('Encrypt value: ' || l_encrypt);
END;
/

--result
Encrypt value: 97ED8C1EE5846E622BEB372600EEA263

DECLARE
    l_input RAW(100) := HEXTORAW('596F757253656372657444617461');
    l_key RAW(30) := HEXTORAW('30313233343536373839414243444546');
    l_iv RAW(30) := HEXTORAW('30313233343536373839414243444546');
    l_encrypt RAW(2000);
BEGIN 
    l_encrypt := DBMS_CRYPTO.ENCRYPT(
                    src => l_input,
                    typ => DBMS_CRYPTO.ENCRYPT_AES128 + DBMS_CRYPTO.CHAIN_CBC + DBMS_CRYPTO.PAD_PKCS7,
                    key  => l_key,
                    iv => l_iv
                );

    DBMS_OUTPUT.PUT_LINE('Encrypt value: ' || l_encrypt);
END;
/

-- result
Encrypt value: 05B75EB2703F4CD71A02CA7A6EFB4640
```

## Hash

```plsql
DBMS_CRYPTO.Hash (
   src IN RAW,
   typ IN INTEGER)
 RETURN RAW;
```

Hash函数用于对数据进行单向散列。

| 参数    | 描述  |
| ------- |----------------|
| src | 待哈希散列的源数据，必须为RAW类型或能隐式转换为RAW类型的其他类型，且不能为NULL。|
| typ  |使用的哈希算法，必须为INTEGER类型。|

本函数遵循如下规则：

- src允许为空，src为空时函数返回空。

- typ不能为空，需通过使用包中的常量进行指定。

示例

```sql
DECLARE
    l_input RAW(100) := HEXTORAW('596F757253656372657444617461');
    l_hash RAW(100);
BEGIN

    l_hash := DBMS_CRYPTO.HASH(
                    src => l_input,
                    typ => DBMS_CRYPTO.HASH_SH256
                );

    DBMS_OUTPUT.PUT_LINE('Hash value: ' || l_hash);
END;
/

-- result
Hash value: F27F3BE39C013B4E7ABE2130AFC996B1CFF32E18AED9EB206162CCE29F763F1C
```