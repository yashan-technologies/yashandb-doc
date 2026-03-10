YashanDB中的BOX2D类型是数据库内置的一种自定义类型，用于存储和访问ST_GEOMETRY的二维边界框。

## 存储属性

| 类型 | 字节长度 |
|--------|------|
| BOX2D  | 32   |

## 定义格式

| 类型 | 格式    | 规则                       |
|--------|-------| -------------------------- |
| BOX2D  | BOX2D | 自定义对象类型，无需指定size |

## BOX2D属性

| 属性名称 | 类型     | 长度   |
|----------|--------|------|
| XMIN     | DOUBLE | 8字节  |
| XMAX     | DOUBLE | 8字节     |
| YMIN     | DOUBLE | 8字节 |
| YMAX     | DOUBLE | 8字节     |

## 使用规则

### 使用限制

对于BOX2D类型数据的使用限制如下：

- 不能作为分区表的分区键。
- 仅适用于HEAP表。

### 使用格式

BOX2D类型可通过OBJECT方法或者ST_EXTENT函数生成，并通过`表别名.列名.属性名`的方式进行查询。

示例（HEAP表）

```sql
-- 1.创建包含BOX2D类型的表box_table
CREATE TABLE box_table (id INT, box BOX2D);

-- 2.向表中插入BOX2D对象
INSERT INTO box_table VALUES(1, BOX2D(1,2,3,4));

-- 3.查询BOX2D数据
SELECT TB.box.xmin XMIN FROM box_table TB;

XMIN
----------- 
1.0E+000
```