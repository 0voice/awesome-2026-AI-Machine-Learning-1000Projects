Transformer 是 2017 年由 Google 提出的**纯注意力机制的序列建模模型**，**完全抛弃 RNN/CNN**，靠自注意力实现并行计算和长距离依赖建模，是现在 NLP、多模态大模型的基础架构。

### 1. 核心架构
标准 Transformer 是 **Encoder-Decoder 结构**：
- **Encoder（编码器）**：堆叠 N 层，用于**理解、特征提取**
  每层 = 多头自注意力 + 前馈网络 + 残差连接 + 层归一化
- **Decoder（解码器）**：堆叠 N 层，用于**生成**
  每层 = 掩码自注意力 + 编码器-解码器注意力 + 前馈网络 + 残差+层归一化

### 2. 最核心的 3 个机制
1. **自注意力（Self-Attention）**
   计算序列中每个 token 与其他所有 token 的关联权重，**直接捕捉全局依赖**，没有长距离衰减。
   通过 Q、K、V 计算：
   \[
   \text{Attention}(Q,K,V) = \text{softmax}\left(\frac{QK^T}{\sqrt{d_k}}\right)V
   \]

2. **多头注意力（Multi-Head Attention）**
   把 Q、K、V 映射到多个子空间，**并行学习不同类型的关联**，提升模型表达能力。

3. **位置编码（Positional Encoding）**
   Transformer 没有时序/卷积结构，用正弦余弦编码**加入位置信息**，让模型知道序列顺序。

### 3. 最大优势（面试必说）
1. **并行训练**：不像 RNN 串行处理，Transformer 可以同时计算整个序列，训练速度极快。
2. **长依赖能力强**：注意力直接建模全局关系，解决 RNN 长序列梯度消失问题。
3. **通用性极强**：
   - 只用 Encoder → 做理解任务（BERT）
   - 只用 Decoder → 做生成任务（GPT）
   - Encoder-Decoder → 翻译、摘要等

### 4. 一句话总结
Transformer 是**基于自注意力、可并行、能捕捉长依赖**的通用序列模型，奠定了现代大语言模型和多模态模型的基础。
