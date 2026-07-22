DBMS_LLM包提供了一组内置的函数与过程，用于管理和使用AI模型。通过DBMS_LLM包，用户可以创建、删除和修改AI模型，并通过AI_EMBED、AI_COMPLETE、AI_RERANK等内置函数调用模型。

## CREATE_MODEL

```plsql
DBMS_LLM.CREATE_MODEL (
   model_id    IN VARCHAR,
   base_url    IN VARCHAR,
   model_name  IN VARCHAR,
   model_type  IN VARCHAR,
   protocol    IN VARCHAR,
   api_key     IN VARCHAR DEFAULT 'NULL',
   attributes  IN JSON DEFAULT 'NULL');
```

CREATE_MODEL过程用于注册一个新的AI模型到数据字典中。

> **Note**: 
>
> 所有参数均不可包含非ASCII字符。


| 参数 | 描述 |
| :---- | :---- |
| model_id | 模型ID，用于唯一标识一个AI模型。不可为NULL。 |
| base_url | 模型服务的Base URL。不可为NULL。 |
| model_name | 模型名称，用于在API请求中标识实际调用的模型。不可为NULL。 |
| model_type | 模型类型，取值必须为'embedding'、'rerank'或'llm'。不可为NULL。 |
| protocol | 协议类型，当前仅支持'openai'。不可为NULL。 |
| api_key | API密钥，用于Bearer Token认证。缺省为'NULL'，表示不使用认证。 |
| attributes | 模型属性，JSON格式。缺省为'NULL'。 |


示例

```plsql
-- 注册一个嵌入模型
EXEC DBMS_LLM.CREATE_MODEL('my_embed_model', 'http://localhost:8080', 'text-embedding-ada-002', 'embedding', 'openai');

-- 注册一个LLM模型（带API密钥）
EXEC DBMS_LLM.CREATE_MODEL('my_llm_model', 'http://localhost:8080', 'gpt-4', 'llm', 'openai', 'sk-xxx', JSON('{"temperature":0.7,"max_tokens":1000}'));

-- 注册一个重排序模型
EXEC DBMS_LLM.CREATE_MODEL('my_rerank_model', 'http://localhost:8080', 'rerank-model', 'rerank', 'openai', 'NULL');
```

## ALTER_MODEL

```plsql
DBMS_LLM.ALTER_MODEL (
   model_id    IN VARCHAR,
   attributes  IN JSON DEFAULT 'NULL');
```

ALTER_MODEL过程用于修改已注册AI模型的属性信息。修改成功后，模型缓存将被自动失效，下次调用时重新加载。

| 参数 | 描述 |
| :---- | :---- |
| model_id | 待修改的模型ID。不可为NULL。 |
| attributes | 新的模型属性，JSON格式。缺省为'NULL'，表示不修改。 |

示例（单机、共享集群/YAC/分布式集群部署）

```plsql
-- 修改模型属性
EXEC DBMS_LLM.ALTER_MODEL('my_llm_model', '{"temperature":0.7,"max_tokens":200}');
```

## DROP_MODEL

```plsql
DBMS_LLM.DROP_MODEL (
   model_id    IN VARCHAR);
```

DROP_MODEL过程用于从数据字典中删除已注册的AI模型。删除成功后，模型缓存将被自动失效。

| 参数 | 描述 |
| :---- | :---- |
| model_id | 待删除的模型ID。不可为NULL。 |

示例

```plsql
-- 删除模型
EXEC DBMS_LLM.DROP_MODEL('my_embed_model');
```
