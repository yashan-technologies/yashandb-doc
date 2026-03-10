下表展示了所有支持的YashanDB外部数据类型：

| 外部数据类型标识符              | 外部数据类型定义      | 实际外部数据类型定义结构                                                                                                                                    |
|------------------------|---------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| YAC_SQLT_BOOL          | YacBool       | bool                                                                                                                                            |
| YAC_SQLT_TINYINT       | YacInt8       | signed char                                                                                                                                     |
| YAC_SQLT_UTINYINT      | YacUint8      | unsigned char                                                                                                                                   |
| YAC_SQLT_SMALLINT      | YacInt16      | short                                                                                                                                           |
| YAC_SQLT_USMALLINT     | YacUint16     | unsigned short                                                                                                                                  |
| YAC_SQLT_INTEGER       | YacInt32      | int                                                                                                                                             |
| YAC_SQLT_UINTEGER      | YacUint32     | unsigned int                                                                                                                                    |
| YAC_SQLT_BIGINT        | YacInt64      | long long                                                                                                                                       |
| YAC_SQLT_UBIGINT       | YacUint64     | unsigned long long                                                                                                                              |
| YAC_SQLT_FLOAT         | YacFloat      | float                                                                                                                                           |
| YAC_SQLT_DOUBLE        | YacDouble     | double                                                                                                                                          |
| YAC_SQLT_NUMBER        | YacNumber     | #define YAC_NUMBER_SIZE 20<br />typedef struct StYacNumber {<br/>    unsigned char numberPart[YAC_NUMBER_SIZE];<br/>} YacNumber;                |
| YAC_SQLT_DATE          | YacDate       | long long                                                                                                                                       |
| YAC_SQLT_SHORTTIME     | YacShortTime  | long long                                                                                                                                       |
| YAC_SQLT_TIMESTAMP     | YacTimestamp  | #define YAC_TIMESTAMP_SIZE 12<br />typedef struct StYacTimestamp {<br/>    unsigned char timestampPart[YAC_TIMESTAMP_SIZE];<br/>} YacTimestamp; |
| YAC_SQLT_TIMESTAMP_LTZ | YacTimestamp  | #define YAC_TIMESTAMP_SIZE 12<br />typedef struct StYacTimestamp {<br/>    unsigned char timestampPart[YAC_TIMESTAMP_SIZE];<br/>} YacTimestamp; |
| YAC_SQLT_TIMESTAMP_TZ  | YacTimestamp  | #define YAC_TIMESTAMP_SIZE 12<br />typedef struct StYacTimestamp {<br/>    unsigned char timestampPart[YAC_TIMESTAMP_SIZE];<br/>} YacTimestamp; |
| YAC_SQLT_YM_INTERVAL   | YacYMInterval | int                                                                                                                                             |
| YAC_SQLT_DS_INTERVAL   | YacDSInterval | long long                                                                                                                                       |
| YAC_SQLT_CHAR          | YacChar*      | char*                                                                                                                                           |
| YAC_SQLT_VARCHAR       | YacChar*      | char*                                                                                                                                           |
| YAC_SQLT_BINARY        | YacChar*      | char*                                                                                                                                           |
| YAC_SQLT_CLOB          | YacLobLocator | 内部结构                                                                                                                                            |
| YAC_SQLT_BLOB          | YacLobLocator | 内部结构                                                                                                                                            |
| YAC_SQLT_NCLOB         | YacLobLocator | 内部结构                                                                                                                                            |
| YAC_SQLT_BIT           | YacChar*      | char*                                                                                                                                           |
| YAC_SQLT_ROWID         | YacChar*      | #define YAC_ROWID_SIZE 16<br />typedef struct StYacRowId {<br/>    unsigned char rowIdPart[YAC_ROWID_SIZE];<br/>} YacRowId;                     |
| YAC_SQLT_JSON          | YacLobLocator | 内部结构                                                                                                                                            |
| YAC_SQLT_CHAR2         | YacChar*      | char*                                                                                                                                           |
| YAC_SQLT_VARCHAR2      | YacChar*      | char*                                                                                                                                           |
| YAC_SQLT_BINARY2       | YacChar*      | char*                                                                                                                                           |
| YAC_SQLT_CURSOR        | YacHandle     | void*                                                                                                                                           |
| YAC_SQLT_OBJSTRING     | YacString     | 内部结构                                                                                                                                            |
| YAC_SQLT_OBJRAW        | YacRaw        | 内部结构                                                                                                                                            |
