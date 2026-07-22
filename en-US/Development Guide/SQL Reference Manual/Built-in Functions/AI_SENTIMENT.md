```ebnf
ai_sentiment = AI_SENTIMENT "(" model_id "," plain_text ")".
```

The AI_SENTIMENT function invokes the specified LLM model to perform sentiment analysis on the input text and returns a sentiment score.

Common use cases:

- Social media sentiment monitoring
- User review sentiment analysis
- Customer feedback emotion recognition
- Customer service conversation sentiment analysis
- Market research text sentiment classification

**model_id**

The registered AI model ID, obtained from the MODEL_ID field in the DBA_AI_MODELS view, is a [general expression](../General SQL Syntax/expr). Its value must be of VARCHAR type and cannot be NULL.

**plain_text**

The text to be analyzed, is a [general expression](../General SQL Syntax/expr). Its value must be of VARCHAR type and cannot be NULL. Maximum length is 65534 bytes.

Examples

```sql
-- Invoke LLM model to analyze text sentiment
SELECT AI_SENTIMENT('my_llm_model', 'This product is awesome, very satisfied') FROM DUAL;

-- Invoke LLM model to analyze customer reviews
SELECT AI_SENTIMENT('my_llm_model', 'The service attitude is very poor, very disappointed') FROM DUAL;

-- Error when plain_text is NULL
SELECT AI_SENTIMENT('my_llm_model', NULL) FROM DUAL;
-- ERROR: plain text must not be NULL

-- Error when model_id is NULL
SELECT AI_SENTIMENT(NULL, 'some text') FROM DUAL;
-- ERROR: model_id must not be NULL
```