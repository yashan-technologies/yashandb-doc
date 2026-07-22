```ebnf
ai_rerank = AI_RERANK "(" model_id "," query "," documents ")".
```

AI_RERANK函数调用指定的重排序模型（Rerank）对一组文档进行相关性评分和排序，返回一个JSON格式的结果。

**model_id**

已注册的AI模型ID，可以通过DBA_AI_MODELS视图中的MODEL_ID字段获取，为一个[通用表达式](../通用SQL语法/expr)，其值必须为VARCHAR类型，不可为NULL。

**query**

查询文本，为一个[通用表达式](../通用SQL语法/expr)，其值必须为VARCHAR类型，不可为NULL。最大长度为65534字节。

**documents**

待排序的文档列表，为一个[通用表达式](../通用SQL语法/expr)，其值必须为VARCHAR类型，格式为JSON数组字符串。例如：`'["doc1", "doc2", "doc3"]'`。不可为NULL。

返回值格式为JSON数组，每个元素包含以下字段：

| 字段名 | 类型 | 描述 |
|--------|------|------|
| index | INT | 文档在输入数组中的原始索引 |
| score | FLOAT | 相关性评分 |
| document.text | VARCHAR | 文档内容 |

示例

```sql
-- 对文档进行相关性排序
SELECT AI_RERANK('my_rerank_model',
                  'Database system',
                  '["MySQL is a database","Python is a language","Oracle is a database"]') FROM DUAL;

-- model_id、query或documents为NULL时报错
SELECT AI_RERANK(NULL, 'test', '["doc1"]') FROM DUAL;
-- ERROR: model_id must not be NULL
```