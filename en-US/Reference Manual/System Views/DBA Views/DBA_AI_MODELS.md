This view displays information about all registered AI models.

| Field | Type | Description |
| --- | --- | --- |
| MODEL_ID | VARCHAR(128) | Unique identifier for the model |
| OWNER | VARCHAR(128) | Username of the model owner |
| BASE_URL | VARCHAR(1024) | Base URL of the model service |
| MODEL_NAME | VARCHAR(256) | Model name used in API requests to identify the actual model being called |
| MODEL_TYPE | VARCHAR(32) | Model type<br/>* embedding: Embedding model<br/>* llm: Large Language Model<br/>* rerank: Rerank model |
| PROTOCOL | VARCHAR(32) | Protocol type, currently only supports 'openai' |
| ATTRIBUTES | JSON | Model attributes in JSON format |
| CREATE_TIME | TIMESTAMP | Model registration time |
| MODIFY_TIME | TIMESTAMP | Last modification time of the model |
| INVOKE_COUNT | BINARY_BIGINT | Total number of times the model has been invoked |
| TOTAL_LATENCY | BINARY_BIGINT | Total cumulative latency of model invocations (in microseconds) |
| TOTAL_TOKENS | BINARY_BIGINT | Total number of tokens consumed |