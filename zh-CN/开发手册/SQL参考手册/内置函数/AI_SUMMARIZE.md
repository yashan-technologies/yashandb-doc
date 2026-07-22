```ebnf
ai_summarize = AI_SUMMARIZE "(" model_id "," plain_text ")".
```

AI_SUMMARIZE函数调用指定的LLM模型对输入文本进行摘要处理，返回文本的精简摘要。

常见使用场景：

- 从长文章、报告、文档中提取关键信息
- 生成新闻摘要、会议纪要
- 快速了解长文本的核心内容
- 数据降噪，从大量文本中筛选重要信息

**model_id**

已注册的AI模型ID，可以通过DBA_AI_MODELS视图中的MODEL_ID字段获取，为一个[通用表达式](../通用SQL语法/expr)，其值必须为VARCHAR类型，不可为NULL。

**plain_text**

待摘要的文本，为一个[通用表达式](../通用SQL语法/expr)，其值必须为VARCHAR类型，不可为NULL。最大长度为65534字节。

示例

```sql
-- 调用LLM模型对文本进行摘要
SELECT AI_SUMMARIZE('my_llm_model', '这是一篇关于人工智能发展的详细文章...') FROM DUAL;

-- plain_text为NULL时报错
SELECT AI_SUMMARIZE('my_llm_model', NULL) FROM DUAL;
-- ERROR: plain text must not be NULL

-- model_id为NULL时报错
SELECT AI_SUMMARIZE(NULL, 'some text') FROM DUAL;
-- ERROR: model_id must not be NULL
```