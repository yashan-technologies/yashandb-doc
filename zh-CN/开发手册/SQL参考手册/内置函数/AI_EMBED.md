```ebnf
ai_embed = AI_EMBED "(" model_id "," text ["," dimensions] ")".
```

AI_EMBED函数调用指定的嵌入模型将文本转换为向量（Vector）数据，返回一个VECTOR类型的数据。

**model_id**

已注册的AI模型ID，可以通过DBA_AI_MODELS视图中的MODEL_ID字段获取，为一个[通用表达式](../通用SQL语法/expr)，其值必须为VARCHAR类型，不可为NULL。

**text**

待嵌入的文本，为一个[通用表达式](../通用SQL语法/expr)，其值必须为VARCHAR类型，不可为NULL。最大长度为65534字节。

**dimensions**

可选参数，指定期望的向量维度，为一个[通用表达式](../通用SQL语法/expr)，其值必须为INT类型。该参数当前仅用作校验，暂无实际意义。

当dimension为NULL时，函数使用模型默认的向量维度。

示例

```sql
-- 调用嵌入模型将文本转换为向量
SELECT AI_EMBED('my_embed_model', 'YashanDB is a database') FROM DUAL;

-- 指定向量维度
SELECT AI_EMBED('my_embed_model', 'Hello world', 768) FROM DUAL;

-- model_id或text为NULL时报错
SELECT AI_EMBED(NULL, 'test') FROM DUAL;
-- ERROR: model_id must not be NULL
```