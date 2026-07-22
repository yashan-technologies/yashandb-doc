```ebnf
ai_complete = AI_COMPLETE "(" model_id "," prompt ["," parameters] ")".
```
```ebnf
ai_generate = AI_GENERATE "(" model_id "," prompt ["," parameters] ")".
```

AI_COMPLETE函数调用指定的LLM模型进行对话补全，返回一个VARCHAR类型的结果。

AI_GENERATE函数与AI_COMPLETE函数含义相同。

**model_id**

已注册的AI模型ID，可以通过DBA_AI_MODELS视图中的MODEL_ID字段获取，为一个[通用表达式](../通用SQL语法/expr)，其值必须为VARCHAR类型，不可为NULL。

**prompt**

用户输入的提示文本，为一个[通用表达式](../通用SQL语法/expr)，其值必须为VARCHAR类型，不可为NULL。最大长度为65534字节。

**parameters**

可选参数，指定模型调用的额外参数（如temperature、max_tokens等），为一个[通用表达式](../通用SQL语法/expr)，其值必须为JSON格式的VARCHAR类型。

- 当parameters包含多个参数时，各参数间以逗号分隔，整体作为JSON对象的成员。例如：`"temperature":0.7,"max_tokens":100`。
- 当parameters为NULL或省略时，函数使用模型注册时配置的默认temperature和max_tokens值。
- 如果模型注册时也未配置默认值，则不带额外参数调用API。

示例

```sql
-- 基本调用
SELECT AI_COMPLETE('my_llm_model', 'Translate to English: 你好') FROM DUAL;

-- 指定额外参数
SELECT AI_COMPLETE('my_llm_model', 'Write a poem about database', '"temperature":0.8,"max_tokens":200') FROM DUAL;

-- model_id或prompt为NULL时报错
SELECT AI_COMPLETE(NULL, 'test') FROM DUAL;
-- ERROR: model_id must not be NULL
```