View Help
----

Use the `yaswrap -H` command to view the help information.

```shell
$ yaswrap -H
```

View Version
----

Use the `yaswrap -V` command to view the version information.

```shell
$ yaswrap -V
```

## Command Format

```shell
$ yaswrap iname=input_file [oname=output_file] [keep_comments={yes|no}]
```
**iname**

The PL text file to be wrapped and encrypted, supporting relative path specification.

When `input_file` has no suffix, `.sql` will be added by default, and `input_file.sql` must exist.

The maximum total length of the input_file filename (including suffix) is 251 characters.

**oname**

The output wrapped and encrypted file, supporting relative path specification.

This option can be omitted, and by default, the output will be `input_file.plb` in the input file path.

When `output_file` has no suffix, `.plb` will be added by default.

**keep_comments**

Specifies whether to keep other comments, with a default value of no.

- yes: Only remove comments within the PL text, keeping other comments.

- no: Remove comments within the PL text and other comments.

## Character Set Settings

*yaswrap* supports specifying the character set of the PL source code for subsequent decryption, determined by the client character set parameter CHARACTER_SET configured in `${YASDB_HOME}/client/yasc_env.ini`.

Please ensure that the PL source code character set is consistent with the client character set; otherwise, decryption may fail.

## Digest Algorithm Settings

*yaswrap* supports selecting a digest algorithm during encryption, determined by the client parameter WRAP_ALGORITHM configured in `${YASDB_HOME}/client/yasc_env.ini`.

The optional range is: SHA1, SM3, with SHA1 as the default digest algorithm.

> **Note**: 
>
> - When using *yaswrap* to encrypt PL source code, if there are parts that have already been encrypted with *yaswrap* in the `input_file`, these ciphertexts will be skipped (no re-encryption) and only the remaining plaintext source code will be encrypted according to the currently configured digest algorithm.
>
> - If SM3 algorithm is to be used and OpenSSL tools are required, please refer to [Dependency Preparation](../../Installation and Upgrade/Installation and Deployment/Pre-Installation Preparation/Preparing Dependencies) to check and ensure that the required tools are installed on the server system.