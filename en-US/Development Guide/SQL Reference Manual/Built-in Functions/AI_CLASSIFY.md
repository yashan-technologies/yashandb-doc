```ebnf
ai_classify = AI_CLASSIFY "(" model_id "," query "," labels ")".
```

The AI_CLASSIFY function invokes the specified LLM model to classify the input text and returns the most matching label from the given list of labels.

Common use cases:

- Automatic news article classification
- Spam detection
- Product review classification
- Content moderation and tagging
- Automated customer service ticket routing

**model_id**

The registered AI model ID, obtained from the MODEL_ID field in the DBA_AI_MODELS view, is a [general expression](../General SQL Syntax/expr). Its value must be of VARCHAR type and cannot be NULL.

**query**

The text to be classified, is a [general expression](../General SQL Syntax/expr). Its value must be of VARCHAR type and cannot be NULL. Maximum length is 65534 bytes.

**labels**

The list of classification labels, is a [general expression](../General SQL Syntax/expr). Its value must be of VARCHAR type and cannot be NULL. Labels are separated by commas.

Examples

```sql
-- Invoke LLM model to classify text
SELECT AI_CLASSIFY('my_llm_model', 'This movie is amazing', 'positive,negative,neutral') FROM DUAL;

-- Classify customer service tickets
SELECT AI_CLASSIFY('my_llm_model', 'Cannot login to system', 'technical support,billing,product inquiry,complaint') FROM DUAL;

-- Error when labels is NULL
SELECT AI_CLASSIFY('my_llm_model', 'some text', NULL) FROM DUAL;
-- ERROR: labels must not be NULL

-- Error when query is NULL
SELECT AI_CLASSIFY('my_llm_model', NULL, 'label1,label2') FROM DUAL;
-- ERROR: plain text must not be NULL
```