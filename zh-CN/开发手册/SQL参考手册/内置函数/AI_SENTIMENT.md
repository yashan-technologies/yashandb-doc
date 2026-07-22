```ebnf
ai_sentiment = AI_SENTIMENT "(" model_id "," plain_text ")".
```

AI_SENTIMENT函数调用指定的LLM模型对输入文本进行情感分析，返回情感得分。

常见使用场景：

- 社交媒体舆情监控
- 用户评论情感分析
- 客户反馈情绪识别
- 客服对话情感分析
- 市场调研文本情感判定

**model_id**

已注册的AI模型ID，可以通过DBA_AI_MODELS视图中的MODEL_ID字段获取，为一个[通用表达式](../通用SQL语法/expr)，其值必须为VARCHAR类型，不可为NULL。

**plain_text**

待分析的文本，为一个[通用表达式](../通用SQL语法/expr)，其值必须为VARCHAR类型，不可为NULL。最大长度为65534字节。

示例

```sql
-- 调用LLM模型对文本进行情感分析
SELECT AI_SENTIMENT('my_llm_model', '这个产品太棒了，非常满意') FROM DUAL;

-- 调用LLM模型分析客户评论
SELECT AI_SENTIMENT('my_llm_model', '服务态度很差，很失望') FROM DUAL;

-- plain_text为NULL时报错
SELECT AI_SENTIMENT('my_llm_model', NULL) FROM DUAL;
-- ERROR: plain text must not be NULL

-- model_id为NULL时报错
SELECT AI_SENTIMENT(NULL, 'some text') FROM DUAL;
-- ERROR: model_id must not be NULL
```