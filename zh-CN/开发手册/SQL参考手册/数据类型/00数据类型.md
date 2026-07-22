数据库里所有的数据都归属于某一种数据类型（Data Type），数据类型标识了数据的存储相关属性和对该种数据的操作限制。

当用户输入一个数据时，系统将按照一定的规则识别其所属数据类型，具体可参考[字面量](../基本SQL元素/字面量)描述。

当创建表对象时，对列字段的声明必须指定一种数据类型，依据这种数据类型，该列上的所有数据都应满足其存储属性要求，如长度、精度等，才能被成功插入到表中。YashanDB同时提供了数据类型的隐式转换功能，当用户输入数据与字段上定义的数据类型不一致时，系统将先进行数据类型转换，例如将字符型'100'转换为数值型100，此时插入的数据可以满足存储要求，操作成功；对于数据类型无法转换的，例如无法将字符型'aaa'转换为数值型数据，此时才返回插入数据失败。

当数据被用于表达式计算时，其类型也对计算的操作产生限制，符合限制条件的数据类型才能成功执行计算，例如数学类计算要求其参数均为数值型、日期类计算要求其参数均为日期时间型。但如果用户显式指定了数据转换，或者根据YashanDB的隐式转换规则可以成功执行转换，则这些跨数据类型的表达式计算是可以成功执行的。

在一些其他场景中，例如数据被用于函数的参数传递，而其类型与函数定义的参数类型不一致时，系统均会先执行数据类型的隐式转换，以保证最大的兼容性和计算能力。

基于某些数据类型在其属性和操作限制上的共同点，YashanDB的数据类型可以划分为如下几大类：

- [数值型](数值型)：与数字（Numeric）相关的数据属于此类型，具体包含TINYINT、SMALLINT、INT/INTEGER、BIGINT、NUMBER、FLOAT、DOUBLE、BIT等数据类型。
- [字符型](字符型)：与文本字符（Character）相关的数据属于此类型，具体包含CHAR、VARCHAR等数据类型。
- [日期时间型](日期时间型)：与日期时间（Datetime）相关的数据属于此类型，具体包含DATE、TIME、TIMESTAMP、TIMESTAMP WITH LOCAL TIMEZONE、TIMESTAMP WITH TIME ZONE、INTERVAL YEAR TO MONTH、INTERVAL DAY TO SECOND等数据类型。
- [布尔型](布尔型)：与布尔逻辑（Boolean）相关的数据属于此类型，具体为Boolean数据类型。
- [大对象型](大对象型)：与大二进制、大文本等大对象（Large Object）相关的数据属于此类型，具体包含CLOB、BLOB等数据类型。
- [ROWID/UROWID](ROWID UROWID)：用于表示某条行记录的物理地址的数据属于此类型。
- [RAW](RAW)：与VARCHAR类似的可变长度数据类型，多被用于存储二进制对象。
- [JSON](JSON)：与JSON相关的可变长度数据类型，多被用于存储JSON格式的二进制对象。
- [ST_GEOMETRY](ST_GEOMETRY)：与GIS相关的空间数据类型，具体包含POINT、LINESTRING、POLYGON、MULTIPOINT、MULTILINESTRING、MULTIPOLYGON等数据类型。
- [XMLTYPE](XMLTYPE)：与XML相关的数据库内置的一种自定义类型。
- [BOX2D](BOX2D)：与ST_GEOMETRY相关的空间数据类型，用于表示ST_GEOMETRY的二维边界框。
- [BFILE](BFILE)：与BFILE相关的二进制大对象类型，用于处理存储在数据库外部的二进制文件。

本章节将按上述列举大类分别阐述YashanDB内置的所有数据类型的定义以及它们之间的转换规则。

> **Note**: 
>
> 以下数据类型只适用于HEAP表：
> - NCLOB
> - NCHAR
> - NVARCHAR
> - FLOAT（当USE_NATIVE_TYPE为FALSE时）
> - XMLTYPE
> - BOX2D
> - ST_GEOMETRY
> - TIMESTAMP WITH LOCAL TIME ZONE
> - TIMESTAMP WITH TIME ZONE
> - [用户自定义类型](用户自定义类型)
> - BFILE
