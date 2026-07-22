故障诊断视图，显示相关健康检查成果。

|  字段| 类型| 说明|
| --- | --- | --- |
| FINDING\_ID | INTEGER | 表示结果的唯一ID |
| RUN\_ID | INTEGER | 创建此结果的运行的ID |
| NAME | VARCHAR(33) | 调查结果的名称 |
| TIME\_DETECTED | DATE | 检测到此发现的时间 |
| DESCRIPTION | VARCHAR(1024) | 调查结果说明 |
| DAMAGE\_DESCRIPTION | VARCHAR(512) | 调查结果的可能损坏描述 |
