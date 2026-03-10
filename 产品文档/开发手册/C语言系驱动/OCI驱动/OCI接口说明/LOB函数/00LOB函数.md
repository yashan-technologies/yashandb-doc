OCI驱动提供通过LOB数据类型实现对大型数据的存储和检索的功能。LOB数据类型用于存储大量的二进制或字符数据，例如图像、音频、视频、文档等。在数据库中，LOB通常被分为两种类型：BLOB（Binary Large Object）和CLOB（Character Large Object）。
- BLOB类型用于存储二进制数据，可以存储图像、音频、视频等文件。
- CLOB类型用于存储字符数据，可以存储文本文件、XML文件等。

OCI驱动提供了一组API函数或方法来执行LOB数据类型相关的操作，包括插入、更新、删除和查询LOB数据等。插入或更新LOB数据时，可以使用API函数或方法将LOB数据从文件或内存缓冲区写入数据库。查询或检索LOB数据时，可以使用API函数或方法从数据库中读取LOB数据并将其保存到文件或内存缓冲区中。

对LOB数据执行操作时一般需要指定LOB的句柄或标识符，LOB句柄是指向LOB数据的引用，通过指定LOB句柄可以获取该LOB数据的长度、读取该LOB数据的内容、写入LOB数据等。

| 接口名称               | 接口说明                                 |
|-------------------|--------------------------------------|
| [OCILobGetLength](OCILobGetLength)    |  获取LOB的长度      |
| [OCILobRead](OCILobRead) | 将LOB读入缓冲区 |
| [OCILobWrite](OCILobWrite) |将缓冲区写入LOB  |
| [OCILobIsEqual](OCILobIsEqual) |判断两个LOB是否相等 |
| [OCILobCharSetForm](OCILobCharSetForm) | 获取LOB的字符集形式 |
| [OCILobCharSetId](OCILobCharSetId) | 获取LOB的字符集ID |
| [OCILobGetChunkSize](OCILobGetChunkSize) |获取LOB的块大小  |
| [OCILobCreateTemporary](OCILobCreateTemporary) | 创建临时LOB |
| [OCILobIsTemporary](OCILobIsTemporary) | 判断LOB是否为临时LOB |
| [OCILobFreeTemporary](OCILobFreeTemporary) | 释放临时LOB |
| [OCILobRead2](OCILobRead2) | 将LOB读入缓冲区 |
| [OCILobGetLength2](OCILobGetLength2) | 获取LOB的长度|
| [OCILobOpen](OCILobOpen) | 在指定的模式下打开LOB|
| [OCILobClose](OCILobClose) | 关闭以前打开的LOB|
| [OCILobWrite2](OCILobWrite2) | 将缓冲区写入LOB|
| [OCILobTrim2](OCILobTrim2) | 将LOB值截断为较短的长度|
| [OCILobLocatorIsInit](OCILobLocatorIsInit) | 判断LOB是否初始化|
| [OCILobIsOpen](OCILobIsOpen) | 判断LOB是否打开|
