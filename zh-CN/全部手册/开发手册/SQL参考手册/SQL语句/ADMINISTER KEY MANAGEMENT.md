通用描述
----

ADMINISTER KEY MANAGEMENT为透明数据加密提供了统一的密钥管理接口，用于管理钱包和主密钥。

该语句不适用于存算一体分布式集群部署。

>**Caution**:
>
> 请务必牢记[钱包](#createkeystore)的密码并及时对钱包进行备份，钱包相关数据丢失会导致数据库不可用。

语句定义
----

**administer key management::=**

```ebnf+diagram
syntax::=  ADMINISTER KEY MANAGEMENT (keystore_management_clauses
|key_management_clauses)
```

**[keystore\_management\_clauses](#keystoremanagementclauses)::=**

```ebnf+diagram
syntax::= (create_keystore
| open_keystore
| close_keystore
| backup_keystore)
```

**[create\_keystore](#createkeystore)::=**

```ebnf+diagram
syntax::= CREATE KEYSTORE [ "'" keystore_location "'" ] IDENTIFIED BY keystore_password
```

**[open\_keystore](#openkeystore)::=**

```ebnf+diagram
syntax::= SET KEYSTORE OPEN
  [ FORCE KEYSTORE ]
  IDENTIFIED BY ( EXTERNAL STORE | keystore_password ) 
```

**[close\_keystore](#closekeystore)::=**

```ebnf+diagram
syntax::= SET KEYSTORE CLOSE
  [ IDENTIFIED BY ( EXTERNAL STORE | keystore_password ) ] 
```

**[backup_keystore](#backupkeystore)::=**

```ebnf+diagram
syntax::= BACKUP KEYSTORE [ USING "'" backup_identifier "'" ]
  [ FORCE KEYSTORE ]
  IDENTIFIED BY ( EXTERNAL STORE | keystore_password )
  [ TO "'" keystore_location "'" ] 
```

**[key\_management\_clauses](#keymanagementclauses)::=**

```ebnf+diagram
syntax::= ( set_key
  | create_key
  | use_key
  | set_key_tag
  ) 
```

**[set\_key](#setkey)::=**

```ebnf+diagram
syntax::= SET [ ENCRYPTION ] KEY [ mkid_mk | mk ]
  [ USING TAG "'" tag "'" ]
  [ USING ALGORITHM "'" encrypt_algorithm "'" ]
  [ FORCE KEYSTORE ]
  IDENTIFIED BY ( EXTERNAL STORE | keystore_password )
  WITH BACKUP [ USING "'" backup_identifier "'" ] 
```

**[create\_key](#createkey)::=**

```ebnf+diagram
syntax::= CREATE [ ENCRYPTION ] KEY [ mkid_mk | mk ]
  [ USING TAG "'" tag "'" ]
  [ USING ALGORITHM "'" encrypt_algorithm "'" ]
  [ FORCE KEYSTORE ]
  IDENTIFIED BY ( EXTERNAL STORE | keystore_password )
  WITH BACKUP [ USING "'" backup_identifier "'" ] 
```

**[use\_key](#usekey)::=**

```ebnf+diagram
syntax::= USE [ ENCRYPTION ] KEY "'" key_id "'"
  [ USING TAG "'" tag "'" ]
  [ FORCE KEYSTORE ]
  IDENTIFIED BY ( EXTERNAL STORE | keystore_password )
  WITH BACKUP [ USING "'" backup_identifier "'" ] 
```

**[set_key_tag](#setkeytag)::=**

```ebnf+diagram
syntax::= SET TAG "'" tag "'" FOR "'" key_id "'" 
  [ FORCE KEYSTORE ]
  IDENTIFIED BY ( EXTERNAL STORE | keystore_password )
  WITH BACKUP [ USING "'" backup_identifier "'" ] 
```

<span id="keystoremanagementclauses" name="keystoremanagementclauses" class="yaslink"></span>

### 1. keystore\_management\_clauses

该语句用于进行钱包管理操作，包括创建新钱包、打开、关闭或备份已有钱包。

<span id="createkeystore" name="createkeystore" class="yaslink"></span>

#### 1.1. create\_keystore

该语句用于创建钱包，同一个目录中最多允许创建一个钱包。

创建钱包前，应先按需选择使用本地密钥管理或第三方密钥管理：

- [本地密钥管理](#local)：将主密钥持久化在数据库服务器本地的钱包文件中。

- [第三方密钥管理](#kms)：对接第三方提供的密钥管理服务，由第三方保护主密钥，目前仅支持腾讯云KMS。如需使用第三方密钥管理，需先修改配置参数[TDE_CONFIGURATION](../../../参考手册/配置参数.html#TDE_CONFIGURATION)为`KEYSTORE_CONFIGURATION=FILE;KMS=TRUE`。

##### 1.1.1. keystore\_location

指定钱包的路径信息，需具备该路径的读写权限，创建钱包会自动在该路径生成ewallet.p12文件。

创建钱包与配置[WALLET_ROOT](../../../参考手册/配置参数.html#wallet)参数不存在绝对的先后顺序约束，但钱包文件的路径要求如下：

- 若先配置WALLET_ROOT参数再创建钱包：

  - 可直接省略keystore_location，由系统默认在WALLET_ROOT路径下创建一个子目录`tde`，并在该子目录下生成ewallet.p12文件。

  - 若指定keystore_location，必须指定为WALLET_ROOT路径的子目录`WALLET_ROOT路径/tde`。

- 若先创建钱包再配置WALLET_ROOT参数：

  必须指定keystore_location，建议指定为规划好的WALLET_ROOT路径的子目录`WALLET_ROOT路径/tde`，否则在创建完钱包后[打开钱包](#openkeystore)前需要将相关文件移至`WALLET_ROOT路径/tde`目录下。

##### 1.1.2. IDENTIFIED BY keystore\_password

该语句用于指定钱包的认证信息。

<span id="local" name="local" class="yaslink"></span>

**本地密钥管理**

创建本地密钥管理的钱包时，认证信息即为钱包文件ewallet.p12的密码。

示例（单机、共享集群部署）

```sql
-- 配置WALLET_ROOT参数
ALTER SYSTEM SET WALLET_ROOT='/data/yashan/wallet' scope=spfile;

-- 重启数据库使配置生效

-- 场景1：不指定keystore_location创建本地密钥管理钱包，系统会自动创建子目录/tde
ADMINISTER KEY MANAGEMENT CREATE KEYSTORE IDENTIFIED BY admin123;

-- 场景2：指定keystore_location创建本地密钥管理钱包，必须指定至tde层级
ADMINISTER KEY MANAGEMENT CREATE KEYSTORE '/data/yashan/wallet/tde' IDENTIFIED BY admin123;
```

<span id="kms" name="kms" class="yaslink"></span>

**第三方密钥管理**

创建使用第三方密钥管理的钱包时，认证信息为KMS相关配置信息，格式为`<schema>://<host>/<path/to/resource>?<query>#<hash>`。

| 配置项| 说明|
|--------------------|--------------------------------------|
| \<schema\> | 固定为kms。 |
| \<host\>  |指定KMS类型，目前仅支持腾讯云KMS，取值只能为tencent，不区分大小写。|
| \<path/to/resource\>  |预留配置，目前无需配置。|
| \<query\>  |  配置信息键值对，多个键值对间使用&分隔，键值对包括：<br/>- password：钱包文件ewallet.p12的密码。<br/>  - skipCheck：是否跳过KMS自检，取值不区分大小写，取值可为[true&#124;false]，默认为FALSE，即不跳过。若不跳过自检，在开启钱包时会对配置的KMS服务进行检查，测试某个密文可否正常解密，解密正常则认为KMS服务配置正常。<br/>  - KMS服务对接信息：例如腾讯云KMS服务的keyId（在KMS中所创建的根密钥ID）、secretId（创建根密钥的腾讯云账号的云API密钥ID）以及secretKey（创建根密钥的腾讯云账号的云API密钥值）。  |
| \<hash\> | KMS服务的API地址，例如`https://kms.tencentcloudapi.com/`。 |


示例（单机、共享集群部署）

```sql
-- 配置TDE_CONFIGURATION参数和WALLET_ROOT参数
ALTER SYSTEM SET TDE_CONFIGURATION='KEYSTORE_CONFIGURATION=FILE;KMS=TRUE' WALLET_ROOT='/data/yashan/wallet' scope=spfile;

-- 重启数据库使配置生效

-- 场景1：不指定keystore_location创建本地密钥管理钱包，系统会自动创建子目录/tde
ADMINISTER KEY MANAGEMENT CREATE KEYSTORE IDENTIFIED BY "kms://tencent/?password=admin123&keyId=6xxxxxxx-xxxx-xxxx-xxxx-5xxxxxxxxc09&secretId=AKID********************************&secretKey=********************************#https://kms.tencentcloudapi.com/";

-- 场景2：指定keystore_location创建使用KMS的钱包
ADMINISTER KEY MANAGEMENT CREATE KEYSTORE '/data/yashan/wallet/tde' IDENTIFIED BY "kms://tencent/?password=admin123&keyId=6xxxxxxx-xxxx-xxxx-xxxx-5xxxxxxxxc09&secretId=AKID********************************&secretKey=********************************#https://kms.tencentcloudapi.com/";
```

<span id="openkeystore" name="openkeystore" class="yaslink"></span>

#### 1.2. open\_keystore

该语句用于打开`WALLET_ROOT路径/tde`目录下的钱包，只能通过IDENTIFIED BY keystore_password指定钱包的认证信息。

该语句的使用规则如下：

- 需确保已正确配置WALLET_ROOT参数、已生成ewallet.p12等钱包相关文件且存放在`WALLET_ROOT路径/tde`目录。

- 在主备部署中，需在所有主备库都打开钱包后，再启动数据库到MOUNT阶段或OPEN阶段，否则可能导致备库加密日志回放失败，备库将不可用。当遇到此场景时，日志会报错"wallet is not open"，需连接对应的备库打开钱包。

- 在共享集群部署中，需在所有实例都打开钱包后，再启动数据库到MOUNT阶段或OPEN阶段，否则可能导致加密日志回放失败，备节点将不可用。当遇到此场景时，日志会报错"wallet is not open"，需连接对应的实例打开钱包。

FORCE KEYSTORE和IDENTIFIED BY EXTERNAL STORE仅用于语法兼容，无实际含义。

示例（单机、共享集群部署）

```sql
ADMINISTER KEY MANAGEMENT SET KEYSTORE OPEN IDENTIFIED BY Your_keystore_password;
```

<span id="closekeystore" name="closekeystore" class="yaslink"></span>

#### 1.3. close\_keystore

该语句用于关闭WALLET_ROOT的子目录`tde`下的钱包，只能通过IDENTIFIED BY keystore_password指定钱包的认证信息。

IDENTIFIED BY EXTERNAL STORE仅用于语法兼容，无实际含义。

示例（单机、共享集群部署）

```sql
ADMINISTER KEY MANAGEMENT SET KEYSTORE CLOSE IDENTIFIED BY Your_keystore_password;
```

<span id="backupkeystore" name="backupkeystore" class="yaslink"></span>

#### 1.4. backup\_keystore

该语句用于备份WALLET_ROOT的子目录`tde`下的钱包。

需在执行该语句前先[打开钱包](#openkeystore)，或通过指定FORCE KEYSTORE关键字强制打开钱包，否则该语句会执行失败。

##### 1.4.1. USING 'backup\_identifier'

指定钱包备份文件的标识，可省略。

##### 1.4.2. TO 'keystore\_location'

指定钱包的备份路径，需具备该路径的读写权限。可省略，省略则备份在原文件所在目录中。

##### 1.4.3. FORCE KEYSTORE

指定该关键字可强制打开钱包，且当前子句执行结束后钱包继续保持打开状态。

若当前未[打开钱包](#openkeystore)，则必须指定FORCE KEYSTORE关键字。

##### 1.4.4. IDENTIFIED BY keystore\_password

指定目标钱包的认证信息。

IDENTIFIED BY EXTERNAL STORE仅用于语法兼容，无实际含义。

示例（单机、共享集群部署）

```sql
ADMINISTER KEY MANAGEMENT BACKUP KEYSTORE IDENTIFIED BY Your_keystore_password;
```

<span id="keymanagementclauses" name="keymanagementclauses" class="yaslink"></span>

### 2. key\_management\_clauses

该语句用于管理主密钥，包括[创建并激活主密钥](#setkey)、[创建但不激活主密钥](#createkey)、[激活主密钥](#usekey)以及[为主密钥设置标签](#setkeytag)。

需在执行该语句前先[打开钱包](#openkeystore)，或通过指定FORCE KEYSTORE关键字强制打开钱包，否则该语句会执行失败。

<span id="setkey" name="setkey" class="yaslink"></span>

#### 2.1. set\_key

该语句用于在WALLET_ROOT的子目录`tde`下的钱包中设置（等价于创建+激活）第一个主密钥，或轮换（更新）已有主密钥。

该语句的使用规则如下：

- 必须在数据库OPEN阶段下执行该语句。

- 共享集群部署中，如果多实例间密钥路径相同，在一个实例上更新了主密钥后，需关闭其它实例上的钱包再重新打开钱包，以便让所有实例间的主密钥保持一致。

ENCRYPTION、IDENTIFIED BY EXTERNAL STORE仅用于语法兼容，无实际含义。

>**Note**:
>
>若SET KEY语句执行过程中出现了掉电情况，有可能出现钱包文件写一半的问题，此时数据库可能无法拉起。当遇到该问题时，数据库从机制上保证写钱包文件前备份密钥文件已经完成，需要人工把备份的密钥文件恢复成当前使用的文件。

##### 2.1.1. mkid\_mk|mk|

用于指定钱包主密钥ID所需的值（MKID）和钱包主密钥所需的值（MK），可指定MKID和MK、仅指定MK或全省略。

mkid_mk表示同时指定MKID和MK，格式为`MKID:MK`。

mk表示仅指定MK，数据库会自动生成一个MKID，以便跟踪具有指定的值MK的主密钥。

省略表示MKID与MK全不指定，数据库会自动生成MKID和MK。

- MKID为一个16字节长度的随机值，采用十六进制字符串表示，不能与数据库中已有MKID重复。

- MK为一个32字节长度的随机值，采用十六进制字符串表示。

若需指定MKID或MK，请确保使用有效值，建议使用openssl工具生成MKID和MK。

- 如果MKID无效，例如长度错误，或为一串零，将报错：invalid master key identifier or master key value。

- 如果MKID重复，将报错：master key identifier exists in the keystore。

- 如果MKID无效或MK无效，将报错：invalid master key identifier or master key value。

##### 2.1.2. USING TAG 'tag'

该语句用于为主密钥关联标签，标签最长为4000个字符。

##### 2.1.3. USING ALGORITHM 'encrypt\_algorithm'

该语句用于指定加密主密钥的加密算法，仅支持AES256。

##### 2.1.4. FORCE KEYSTORE

指定该关键字可强制打开钱包，且当前子句执行结束后钱包继续保持打开状态。

若当前未[打开钱包](#openkeystore)，则必须指定FORCE KEYSTORE关键字。

##### 2.1.5. IDENTIFIED BY keystore\_password

该语句用于指定目标钱包的认证信息。

##### 2.1.6. WITH BACKUP [USING 'backup\_identifier']

指定该关键字用于在创建新的主密钥之前先备份钱包，必须填写，否则报错。

备份文件的名称默认为`ewallet_<timestamp>.p12`，其中timestamp以UTC格式表示。指定`USING 'backup_identifier'`可将自定义备份文件名称格式为`ewallet_<timestamp>_<backup_identifier>.p12`。

备份文件保存在原ewallet.p12文件的同一目录下。

示例（单机、共享集群部署）

```shell
-- 使用openssl工具生成MKID
$ openssl rand -hex 16
1e3eeec2f7cc8af9a919517216e8057d

-- 使用openssl工具生成MK
$ openssl rand -hex 32
d8ab781a44b864099a5e607a2063b265d7703f802fcfe7e8334cd8776da44444

-- 登录数据库后，设置主密钥
SQL> ADMINISTER KEY MANAGEMENT SET KEY '1e3eeec2f7cc8af9a919517216e8057d:d8ab781a44b864099a5e607a2063b265d7703f802fcfe7e8334cd8776da44444' USING TAG 'myTag' IDENTIFIED BY Your_keystore_password WITH BACKUP;
```

<span id="createkey" name="createkey" class="yaslink"></span>

#### 2.2. create\_key

该语句用于创建一个新的主密钥。创建完成后，主密钥无法直接使用，还需执行[use_key](#usekey)语句进行激活。

语法及规则同[set_key](#setkey)。

示例（单机、共享集群部署）

```sql
ADMINISTER KEY MANAGEMENT CREATE KEY '1e3eeec2f7cc8af9a919517216e8057d:d8ab781a44b864099a5e607a2063b265d7703f802fcfe7e8334cd8776da44444' USING TAG 'myTag' IDENTIFIED BY Your_keystore_password WITH BACKUP;
```

<span id="usekey" name="usekey" class="yaslink"></span>

#### 2.3. use\_key

该语句用于激活已经存在的主密钥，key_id需指定为目标主密钥的ID（可通过查询[V$ENCRYPTION_KEYS](../../../参考手册/系统视图/动态视图/V$ENCRYPTION_KEYS)视图的KEY_ID字段获取）。

其他语法及规则同[set_key](#setkey)。

示例（单机、共享集群部署）

```sql
SELECT KEY_ID,TAG FROM V$ENCRYPTION_KEYS;

KEY_ID                                                           TAG
---------------------------------------------------------------- ----------------------------------------------------------------
AR4+7sL3zIr5qRlRchboBX0AAAAAAAAAAAAAAAAAAAAAAAAAAAAA             myTag

ADMINISTER KEY MANAGEMENT USE ENCRYPTION KEY 'AR4+7sL3zIr5qRlRchboBX0AAAAAAAAAAAAAAAAAAAAAAAAAAAAA' USING TAG 'newTag'  IDENTIFIED BY Your_keystore_password WITH BACKUP;
```

<span id="setkeytag" name="setkeytag" class="yaslink"></span>

#### 2.4. set\_key\_tag

该语句用于为现有主密钥设置标签或更新已有标签，key_id需指定为目标主密钥的ID（可通过查询[V$ENCRYPTION_KEYS](../../../参考手册/系统视图/动态视图/V$ENCRYPTION_KEYS)视图的KEY_ID字段获取）。

其他语法及规则同[set_key](#setkey)。

示例（单机、共享集群部署）

```sql
SELECT KEY_ID,TAG FROM V$ENCRYPTION_KEYS;

KEY_ID                                                           TAG
---------------------------------------------------------------- ----------------------------------------------------------------
AR4+7sL3zIr5qRlRchboBX0AAAAAAAAAAAAAAAAAAAAAAAAAAAAA             myTag

ADMINISTER KEY MANAGEMENT SET TAG 'mynewTag' FOR 'AR4+7sL3zIr5qRlRchboBX0AAAAAAAAAAAAAAAAAAAAAAAAAAAAA' USING TAG 'newTag' IDENTIFIED BY Your_keystore_password;

SELECT KEY_ID,TAG FROM V$ENCRYPTION_KEYS;

KEY_ID                                                           TAG
---------------------------------------------------------------- ----------------------------------------------------------------
AR4+7sL3zIr5qRlRchboBX0AAAAAAAAAAAAAAAAAAAAAAAAAAAAA             mynewTag
```
