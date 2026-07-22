C驱动LOB功能是通过LOB（Large Object）数据类型实现对大型数据的存储和检索的功能。LOB数据类型用于存储大量的二进制或字符数据，例如图像、音频、视频、文档等。在数据库中，LOB通常被分为两种类型：BLOB（Binary Large Object）和CLOB（Character Large Object）。
- BLOB类型用于存储二进制数据，可以存储图像、音频、视频等文件。
- CLOB类型用于存储字符数据，可以存储文本文件、XML文件等。

C驱动提供了一组API函数或方法来执行LOB数据类型相关的操作，包括插入、更新（不适用于存算一体分布式集群部署）、删除和查询LOB数据等。插入或更新LOB数据时，可以使用API函数或方法将LOB数据从文件或内存缓冲区写入数据库。查询或检索LOB数据时，可以使用API函数或方法从数据库中读取LOB数据并将其保存到文件或内存缓冲区中。

对LOB数据执行操作时一般需要指定LOB的句柄或标识符，LOB句柄是指向LOB数据的引用，通过指定LOB句柄可以获取该LOB数据的长度、读取该LOB数据的内容、写入LOB数据等。

|  接口名称| 接口说明|
|-------------------|--------------------------------------|
| [yacLobGetChunkSize](yacLobGetChunkSize) | LOB协议中，获取LOB的chunkSize |
| [yacLobGetLength](yacLobGetLength)    | LOB协议中，获取LOB数据总长度           |
| [yacLobFreeTemporary](yacLobFreeTemporary) | LOB协议中，通知服务端释放临时LOB |
| [yacLobIsTemporary](yacLobIsTemporary) | LOB协议中，判断LOB是否为临时LOB |
| [yacLobTrim](yacLobTrim) | LOB协议中，截取LOB数据 |
| [yacLobAppend](yacLobAppend) | LOB协议中，将一个LOB追加到另一个LOB末尾 |
| [yacLobCreateTemporary2](yacLobCreateTemporary2) | LOB协议中，通知服务端创建临时LOB |
| [yacLobWriteAppend](yacLobWriteAppend) | LOB协议中，将数据追加到LOB末尾 |
| [yacLobWriteAppendWithCharset](yacLobWriteAppendWithCharset) | LOB协议中，将数据使用特定字符集追加到LOB末尾 |
| [yacLobDescFree2](yacLobDescFree2) | LOB协议中，为loblocator释放空间 |
| [yacLobDescAlloc2](yacLobDescAlloc2) | LOB协议中，为loblocator分配空间 |
| [yacLobWrite2](yacLobWrite2) | LOB协议中，从某个位置将数据插入到LOB |
| [yacLobWrite2WithCharset](yacLobWrite2WithCharset) | LOB协议中，从某个位置将数据使用特定字符集插入到LOB |
| [yacLobRead2](yacLobRead2) | LOB协议中，从某个位置读取LOB数据 |
| [yacLobRead2WithCharset](yacLobRead2WithCharset) | LOB协议中，从某个位置使用特定字符集读取LOB数据 |
