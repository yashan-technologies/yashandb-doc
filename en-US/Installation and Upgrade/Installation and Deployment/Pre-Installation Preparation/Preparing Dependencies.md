In order to ensure the normal installation and operation of YashanDB, please configure the required dependencies in all server environments according to the following sources and minimum version requirements.

## Dependency List

The dependencies required for database operation are shown in the table below.

|Name |Minimum Version Requirement |Open Source License |Source Installation Link |Source Provider |
| ---------- | ------ | ------------------------------------------------------------------ | --------------------------------------------- | -------------------- |
| OpenSSL    | 1.0.0  | [Apache-2.0](https://github.com/openssl/openssl/blob/OpenSSL_1_1_1/LICENSE)      | [https://github.com/openssl/openssl](https://github.com/openssl/openssl)            | OpenSSL              |
| lz4        | 1.8.3  | [BSD 2-Clause](https://github.com/lz4/lz4/blob/v1.9.3/LICENSE)        | [https://github.com/lz4/lz4](https://github.com/lz4/lz4)               | lz4                  |
| zlib       | 1.2.7 | [zlib license](https://zlib.net/zlib_license.html)                    | [https://github.com/madler/zlib](https://github.com/madler/zlib)           | Mark Adler           |
| zstd       | 1.4.4  | [BSD License](https://github.com/facebook/zstd/blob/v1.5.2/LICENSE)   | [https://github.com/facebook/zstd](https://github.com/facebook/zstd)         | Facebook             |
| gmssl      | 3.1.1                      | [Apache-2.0](https://gitee.com/yashan_tech/GmSSL/blob/v3.1.1/LICENSE) | [https://gitee.com/yashan_tech/GmSSL](https://gitee.com/yashan_tech/GmSSL)     | Shenzhen Yashan Technology Co., Ltd. |
| bitshuffle | 0.5.1                      | [MIT](https://gitee.com/yashan_tech/bitshuffle/blob/0.5.1/LICENSE) | [https://gitee.com/yashan_tech/bitshuffle](https://gitee.com/yashan_tech/bitshuffle) | Shenzhen Yashan Technology Co., Ltd. |

> **Note**:
>
> gmssl and bitshuffle are embedded in the YashanDB installation package and do not require manual installation.

The dependencies required for database operation and maintenance tools are shown in the table below.

|Name |Version |Open Source License |Source Installation Link |Source Provider |
| ----- | ------ | ---------------------------------------------------------------- | ------------------------------------------- | ---------- |
| monit | 5.28.0 | [AGPL-3.0](https://www.gnu.org/licenses/agpl-3.0.html)              | [https://bitbucket.org/tildeslash/monit](https://bitbucket.org/tildeslash/monit) | Tildeslash |
| fio   | 3.34   | [GPL-2.0](https://github.com/axboe/fio/blob/fio-3.34/MORAL-LICENSE) | [https://github.com/axboe/fio](https://github.com/axboe/fio)           | Jens Axboe |
| iperf | 2.0.13 | [BSD License](https://github.com/esnet/iperf/blob/master/LICENSE)   | [https://github.com/esnet/iperf](https://github.com/esnet/iperf)         | ESnet      |
| mpathpersist | 0.4.9 or newer  | [GPL-2.0](https://github.com/opensvc/multipath-tools/tree/master/LICENSES)   | [https://github.com/opensvc/multipath-tools/tree/master/mpathpersist](https://github.com/opensvc/multipath-tools/tree/master/mpathpersist)         |   OpenSVC    |
| sshpass | 1.0 or newer  | [GPL-2.0](https://github.com/kevinburke/sshpass/blob/master/LICENSE)   | [https://github.com/kevinburke/sshpass](https://github.com/kevinburke/sshpass)         |   -    |

> **Note**:
>
> - *monit*, *fio* and *iperf* are all embedded in the YashanDB installation package and do not require manual installation.
>
> - Only YAC Deployment and Distributed Cluster Deployment requires *mpathpersist* and *sshpass*:
>
>       During the installation process of YAC and Distributed Cluster, the [fenceResvCheck script](../../../Database Administration/Cluster Management/IO Fencing/Reservation-based IO Fencing.md#fenceResvCheck_usage) will automatically be invoked to check the storage and other devices/environments (Whether detection errors occur or not will not block the installation). This script requires *mpathpersist* and *sshpass*.
>
>   - *mpathpersist*: When the server uses DM-Multipath software to manage storage devices, The script requires *mpathpersist*. Typically, the multipath-tools package is installed along with mpathpersist. You can check if the tool is available by running `mpathpersist --help`.
>       
>   - *sshpass*: This tool is required for multi-server detection if the user specified by -u has not configured no-password login. You can check if the tool is available by running `sshpass -h`. It is generally recommended to configure no-password login.

## Check and Install Dependencies

The following commands are provided as examples for CentOS 7.6 and KylinOS V10.

<span id="openssl" name="openssl"></span>

### libcrypto.so and libssl.so

- **Source**: OpenSSL

- **Version Requirement**: 1.0.0 or above. The versions of libcrypto.so and libssl.so need to be consistent. However, the following scenarios require OpenSSL 1.1.1 or above:
  
  - Using key management functionality
  
  - Using national encryption algorithms (SM2, SM3, SM4, etc.), such as TDE, backup set encryption, PL source code encryption, user password encryption, and built-in functions related to encryption/decryption (CRYPT_ENCRYPT, CRYPT_DECRYPT, CRYPT_HASH, CRYPT_HMAC, CRYPT_SIGN, CRYPT_VERIFY, etc.)

- **Check Method**: `ldconfig -p | grep -E "libcrypto.so|libssl.so"`

- **Installation Method**: `yum install openssl openssl-devel`

> **Caution**:
>
> If libcrypto.so or libssl.so is still not present after installing OpenSSL, you need to manually create a symbolic link.

***Example*** as follows:

```shell
## Query libcrypto.so
# ldconfig -p | grep libcrypto.so
        libcrypto.so.10 (libc6,x86-64) => /lib64/libcrypto.so.10
## If the query result does not have libcrypto.so, create a symbolic link
# ln -s /lib64/libcrypto.so.10 /lib64/libcrypto.so

## Query libssl.so
# ldconfig -p | grep libssl.so
        libssl.so.10 (libc6,x86-64) => /lib64/libssl.so.10
## If the query result does not have libssl.so, create a symbolic link
# ln -s /lib64/libssl.so.10 /lib64/libssl.so

## After creating symbolic links, refresh the dynamic library
# ldconfig
```

### liblz4.so

- **Source**: lz4

- **Check Method**: `ldconfig -p | grep liblz4.so`

- **Installation Method**: `yum install lz4`

### libz.so

- **Source**: zlib

- **Check Method**: `ldconfig -p | grep libz.so`

- **Installation Method**: `yum install zlib`

### libzstd.so

- **Source**: zstd

- **Check Method**: `ldconfig -p | grep libzstd.so`

- **Installation Method**: `yum install libzstd`