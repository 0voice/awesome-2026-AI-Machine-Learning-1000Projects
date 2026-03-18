1.NLP 是变长序列，BN 按批次 + 位置归一化会失效，LN 不受序列长度影响；  
2.BN 对 batch size 敏感，小 batch 统计不准，LN 和 batch size 无关；  
3.LN 训练推理一致，无额外状态，更简单稳定；  
4.NLP 样本独立，LN 只对单样本归一化，更符合任务特性。  
