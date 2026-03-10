The core of information security is data security, and encrypting data is the most fundamental layer of protection. Database encryption technology is the last line of defense for data security, which can prevent data leakage caused by plaintext storage, external hacker attacks, and data theft by internal high-privilege users, fundamentally solving the problem of sensitive data leakage in databases. The encryption program uses passwords or keys to obfuscate data, converting plaintext into ciphertext. Without the decryption key or password, this ciphertext cannot be decrypted, thus maximizing the protection of data security.

Encrypting and decrypting a large volume of data will inevitably bring resource overhead. YashanDB allows users to freely choose from all encryption functionalities, enabling users to select the best balance between performance and security according to their needs. YashanDB provides data encryption protection from the following dimensions:

- [Storage Encryption](Storage Encryption/00Storage Encryption): Ensures the security and confidentiality of various data stored in physical media through password encryption, TDE, and backup set encryption.

- [Trusted Channel](./可信信道/00可信信道): Ensures the confidentiality and integrity of data during transmission based on trusted channels between the database client and server, as well as among the various nodes of the database.

- [Data Masking](Data Masking): Ensures that sensitive information is not leaked by transforming data according to desensitization rules during data sharing, use, or display.

At the same time, YashanDB has a built-in set of encryption and decryption functions that support various industry-standard encryption and hash algorithms, including the AES Advanced Encryption Standard. For detailed information, please refer to [DBMS_CRYPTO](../../开发手册/PL参考手册/内置高级包/DBMS_CRYPTO).