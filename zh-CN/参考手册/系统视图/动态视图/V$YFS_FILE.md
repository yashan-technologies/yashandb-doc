本视图显示YFS中各类文件的信息。

|  字段| 类型| 说明|
|-------|------|------|
|  FILE_NUMBER|INTEGER|磁盘组内的文件编号|
|COMPOUND_INDEX|INTEGER| 含磁盘组ID的文件句柄|
|GROUP_NUMBER|INTEGER|文件所在磁盘组的ID|
|BLOCK_SIZE|INTEGER|文件的块大小，即所在磁盘组的AU_Size|
|BLOCKS|BIGINT|文件的块数量|
|BYTES|BIGINT|文件的字节数|
|REDUNDANCY|VARCHAR(16)| 文件的冗余度<br/>* EXTERNAL：无副本<br/>* NORMAL：2份副本<br/>* HIGH：3份副本|
|CREATION_DATE|DATE|文件的创建日期|
|DELETE_TIME|DATE|文件的删除日期|
|TYPE|INTEGER|文件的类型编号，200以上为YFS元数据类型，类型编号由数据库指定   |
