The DBMS_CRYPTO advanced package provides a set of built-in functions for data encryption and decryption.

The DBMS_CRYPTO advanced package can encrypt and decrypt common data types, including RAW types and other types that can be implicitly converted to RAW types.

DBMS_CRYPTO defines the following encryption algorithms, block cipher chaining modes, padding modes, and hashing algorithms:

|Category |Algorithm/Mode |Description |
|---------|-----------|--------------------------------------|
| Encryption Algorithm | DBMS_CRYPTO.ENCRYPT_AES128    | Uses a 128-bit key, Advanced Encryption Standard |
|                | DBMS_CRYPTO.ENCRYPT_DES           | Uses a 56-bit key                            |
|                | DBMS_CRYPTO.ENCRYPT_3DES          | Encrypts three times on the same block      |
|                | DBMS_CRYPTO.ENCRYPT_3DES_2KEY     | Encrypts three times with two keys on the same block, using a 112-bit key  |
| Block Cipher Chaining Mode | DBMS_CRYPTO.CHAIN_CBC       | Execution flow:<br/>1. Split plaintext into several segments<br/>2. XOR each segment with the initial block or the previous ciphertext<br/>3. Encrypt with the key |
| Padding Mode   | DBMS_CRYPTO.PAD_NONE              | No padding, src must be a multiple of the block size for the encryption, and key must match the block size, otherwise an error is returned |
|                | DBMS_CRYPTO.PAD_PKCS7             | Public Key Cryptography Standards #7 (PKCS#7) padding |
| Hashing Algorithm | DBMS_CRYPTO.HASH_SH256         | SHA-2 series, generates a 256-bit hash value |
|                | DBMS_CRYPTO.HASH_MD5              | Generates a 128-bit hash value              |

## DECRYPT

```plsql
DBMS_CRYPTO.DECRYPT(
   src IN RAW,
   typ IN INTEGER,
   key IN RAW,
   iv  IN RAW DEFAULT NULL)
 RETURN RAW;
```

The DECRYPT function is used to decrypt data.

|Parameter |Description |
| -------- | ------------------------------------------------------------ |
| src         | The ciphertext data to be decrypted, must be RAW type or another type that can be implicitly converted to RAW, and cannot be NULL. |
| typ         | The algorithm and its configuration for encryption and decryption, must be INTEGER type. |
| key         | The key used for decryption, must be RAW type or another type that can be implicitly converted to RAW, and cannot be NULL. |
| iv          | Optional parameter, the initialization vector for block ciphers, must be RAW type or another type that can be implicitly converted to RAW, default is NULL. |

This function follows these rules:

- src is allowed to be NULL; if NULL, the function returns NULL.

- typ cannot be NULL and must be specified using constants in the package, indicating the encryption algorithm, block cipher chaining mode, and padding mode.
    
    When the padding mode is DBMS_CRYPTO.PAD_NONE, ensure that src is a multiple of the block size of the encryption, and key is the block size of the encryption.

***Example***

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

The ENCRYPT function is used to encrypt data.

|Parameter |Description |
| -------- | ------------------------------------------------------------ |
| src         | The source data to be encrypted, must be RAW type or another type that can be implicitly converted to RAW, and cannot be NULL. |
| typ         | The encryption algorithm and its configuration, must be INTEGER type. |
| key         | The key used for encryption, must be RAW type or another type that can be implicitly converted to RAW, and cannot be NULL. |
| iv          | Optional parameter, the initialization vector for block ciphers, must be RAW type or another type that can be implicitly converted to RAW, default is NULL. |

This function follows these rules:

- src is allowed to be NULL; if NULL, the function returns NULL.

- typ cannot be NULL and must be specified using constants in the package, indicating the encryption algorithm, block cipher chaining mode, and padding mode.

    When the padding mode is DBMS_CRYPTO.PAD_NONE, ensure that src is a multiple of the block size of the encryption, and key matches the block size of the encryption.

***Example*** for Heap tables

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

The Hash function is used for one-way hashing of data.

|Parameter |Description |
| ------- |----------------|
| src         | The source data to be hashed, must be RAW type or another type that can be implicitly converted to RAW, and cannot be NULL. |
| typ         | The hashing algorithm used, must be INTEGER type.            |

This function follows these rules:

- src is allowed to be NULL; if NULL, the function returns NULL.

- typ cannot be NULL and must be specified using constants in the package.

***Example***

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
