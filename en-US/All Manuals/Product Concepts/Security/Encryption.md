## TDE

The TDE (Transparent Data Encryption) technology allows the database to automatically perform data encryption and decryption processes without user or application awareness. Data is automatically encrypted when written to storage media, and is stored in cipher text; when read from storage into the database buffer, it is automatically decrypted and presented in plain text. The encryption/decryption process is completely transparent to the application layer, and access control, data operations, and SQL queries at the database layer are not affected in any way.

YashanDB supports TDE at the tablespace level, table level, and column level, with each encryption object having its own independent data key. To enhance security, YashanDB further encrypts and protects data keys through a multi-level key management system.

### Key Management System

YashanDB uses a wallet mechanism and a three-tier key system to manage data keys, ensuring both the security of the keys and the efficiency of data encryption and decryption.

- Wallet: Used for independent external management of database keys.

- Key System:

    - Master Encryption Key (MEK): Also known as the first-level key, it is encrypted by the wallet mechanism and persistently stored in an external wallet file (e.g., ewallet.p12 file) to encrypt/decrypt the global key.

    - Global Encryption Key (GEK): Also known as the second-level key, it is encrypted by the master key and persistently stored in database control files to encrypt/decrypt data keys.

    - Data Encryption Key (DEK): Also known as the third-level key, it is encrypted by the global key and persistently stored in the database to perform encryption and decryption of tablespace, table, and column data.

### Encryption Granularity

YashanDB supports three levels of TDE granularity: tablespace level, table level, and column level.

### Encryption Algorithms

YashanDB supports encryption algorithms AES128 and the national standard algorithm SM4.

## Backup Set Encryption

To ensure the security of backup data files, YashanDB supports specifying an encryption policy during backup.

- Users can choose different encryption algorithms based on their needs; YashanDB supports four encryption algorithms: AES128, AES192, AES256, and national standard SM4.

- The backup encryption key follows the same password policy as the YashanDB user password and utilizes the same key protection mechanism to ensure it cannot be cracked without the plain text password.

- Encryption backups can be performed on control files, data files, redo files, and bucket files.

- Backup encryption can be used with any backup method provided by YashanDB.

- Each backup set of incremental backup must be uniformly either all encrypted or all unencrypted, and the key for each backup set must remain consistent.

- Decryption is validated by entering a password.

## PL Source Code Encryption

By default, the source code of PL objects created by users is stored as text in database tables. Some source code may involve core business logic and other confidential information; encrypting and storing it is an important aspect of enterprise information security.

*yaswrap* is a tool provided by YashanDB specifically for encrypting and wrapping PL source code.

- *yaswrap* can be used to encrypt specified code files and output wrapped files, preventing others from viewing PL source code via database views (e.g., *_SOURCE).

- The encrypted cipher text produced by *yaswrap* can be used to create PL objects directly (the cipher text is also stored in the database), and these PL objects can still function normally; users do not perceive the encryption and decryption processes.

- To recreate PL objects that have been wrapped and encrypted, direct editing in the database is not allowed (direct editing will cause the procedure body to fail to execute properly); the source text file must be modified first and then wrapped again using *yaswrap*.

## Trusted Channels/Transmission Encryption

YashanDB establishes trusted channels for network communication between the client and server, as well as between different nodes of the database server, using encryption protocols such as SSL/TLCP to ensure the confidentiality and integrity of the data transmitted. It also provides authentication for both parties involved in the communication.

- The SSL protocol operates between the application layer (YashanDB session) and the transport layer (TCP), being transparent to the application layer without user awareness.

- YashanDB uses standard X509 digital certificates for authentication during login requests, which can be self-signed or issued by an authoritative institution.

- The SSL/TLCP trusted channel is disabled by default and must be enabled when communication encryption is required. It must also be configured with a digital certificate, or it will prevent the database from starting properly.