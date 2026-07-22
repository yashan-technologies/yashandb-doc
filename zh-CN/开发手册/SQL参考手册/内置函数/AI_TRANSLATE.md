```ebnf
ai_translate = AI_TRANSLATE "(" model_id "," plain_text "," target_language ")".
```

AI_TRANSLATE函数调用指定的LLM模型将输入文本翻译成目标语言，返回翻译后的文本。

常见使用场景：

- 跨语言内容翻译
- 国际化文本处理
- 多语言文档转换
- 跨境电商商品描述翻译

**model_id**

已注册的AI模型ID，可以通过DBA_AI_MODELS视图中的MODEL_ID字段获取，为一个[通用表达式](../通用SQL语法/expr)，其值必须为VARCHAR类型，不可为NULL。

**plain_text**

待翻译的文本，为一个[通用表达式](../通用SQL语法/expr)，其值必须为VARCHAR类型，不可为NULL。最大长度为65534字节。

**target_language**

目标语言，为一个[通用表达式](../通用SQL语法/expr)，其值必须为VARCHAR类型，不可为NULL。例如："英语"、"日语"、"法语"等。

示例

```sql
-- 调用LLM模型将文本翻译成英语
SELECT AI_TRANSLATE('my_llm_model', '你好世界', '英语') FROM DUAL;

-- 调用LLM模型将文本翻译成日语
SELECT AI_TRANSLATE('my_llm_model', '数据库是存储数据的系统', '日语') FROM DUAL;

-- target_language为NULL时报错
SELECT AI_TRANSLATE('my_llm_model', 'some text', NULL) FROM DUAL;
-- ERROR: target language must not be NULL

-- plain_text为NULL时报错
SELECT AI_TRANSLATE('my_llm_model', NULL, '英语') FROM DUAL;
-- ERROR: plain text must not be NULL
```