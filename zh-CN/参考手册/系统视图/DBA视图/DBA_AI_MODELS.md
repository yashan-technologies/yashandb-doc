本视图显示所有已注册的AI模型信息。

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| MODEL_ID | VARCHAR(128) | 模型唯一标识 |
| OWNER | VARCHAR(128) | 模型所有者用户名 |
| BASE_URL | VARCHAR(1024) | 模型服务Base URL |
| MODEL_NAME | VARCHAR(256) | 模型名称，用于API请求中标识实际调用的模型 |
| MODEL_TYPE | VARCHAR(32) | 模型类型<br/>* embedding：嵌入模型<br/>* llm：大语言模型<br/>* rerank：重排序模型 |
| PROTOCOL | VARCHAR(32) | 协议类型，当前仅支持openai |
| ATTRIBUTES | JSON | 模型属性，JSON格式 |
| CREATE_TIME | TIMESTAMP | 模型注册时间 |
| MODIFY_TIME | TIMESTAMP | 模型最后修改时间 |
| INVOKE_COUNT | BINARY_BIGINT | 模型累计调用次数 |
| TOTAL_LATENCY | BINARY_BIGINT | 模型累计调用总延迟（单位：微秒） |
| TOTAL_TOKENS | BINARY_BIGINT | 模型累计消耗Token总数 |
