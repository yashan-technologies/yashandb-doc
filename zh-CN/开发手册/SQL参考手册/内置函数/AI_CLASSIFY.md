```ebnf
ai_classify = AI_CLASSIFY "(" model_id "," query "," labels ")".
```

AI_CLASSIFY函数调用指定的LLM模型对输入文本进行分类，从给定的标签列表中返回一个最匹配的标签。

常见使用场景：

- 新闻文章自动分类
- 垃圾邮件检测
- 商品评论分类
- 内容审核与标签标注
- 客服工单自动分流


**model_id**

已注册的AI模型ID，可以通过DBA_AI_MODELS视图中的MODEL_ID字段获取，为一个[通用表达式](../通用SQL语法/expr)，其值必须为VARCHAR类型，不可为NULL。

**query**

待分类的文本，为一个[通用表达式](../通用SQL语法/expr)，其值必须为VARCHAR类型，不可为NULL。最大长度为65534字节。

**labels**

分类标签列表，为一个[通用表达式](../通用SQL语法/expr)，其值必须为VARCHAR类型，不可为NULL。标签之间使用逗号分隔。

示例

```sql
-- 调用LLM模型对文本进行分类
SELECT AI_CLASSIFY('my_llm_model', '这部电影太精彩了', '正面,负面,中性') FROM DUAL;

-- 对客服工单进行分类
SELECT AI_CLASSIFY('my_llm_model', '无法登录系统', '技术支持,账务问题,产品咨询,投诉') FROM DUAL;

-- labels为NULL时报错
SELECT AI_CLASSIFY('my_llm_model', 'some text', NULL) FROM DUAL;
-- ERROR: labels must not be NULL

-- query为NULL时报错
SELECT AI_CLASSIFY('my_llm_model', NULL, '标签1,标签2') FROM DUAL;
-- ERROR: plain text must not be NULL
```