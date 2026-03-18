公式 + 符号 + 一句话区别，干净不啰嗦。

---

# 1. 先一句话总述
DPO、GRPO 都是**不用奖励模型（RM）**的 LLM 对齐算法，直接用**人类偏好数据**优化策略：
- DPO：处理**二元偏好**（1 个好回答 vs 1 个坏回答）
- GRPO：处理**分组/排序偏好**（一组多个回答按好坏排序）

---

# 2. DPO 公式（Direct Preference Optimization）
### 符号
- \(x\)：输入 prompt
- \(y_w\)：**更优**回答，\(y_l\)：**更差**回答
- \(\pi\)：当前模型
- \(\pi_{\text{ref}}\)：参考模型（一般是 SFT 模型）
- \(\beta\)：温度系数
- \(\sigma\)：sigmoid 函数
- \(\Delta\pi(y) = \log\pi(y|x) - \log\pi_{\text{ref}}(y|x)\)：当前模型相对参考的优势

### 损失函数（**最小化**）
\[
\mathcal{L}_{\text{DPO}} = -\mathbb{E}_{(x,y_w,y_l)}\Big[\log\sigma\big(\beta\cdot\big(\Delta\pi(y_w) - \Delta\pi(y_l)\big)\big)\Big]
\]

**面试口述**：
让**好回答**的策略优势，比**坏回答**的优势更大，用 sigmoid 做二分类偏好学习。

---

# 3. GRPO 公式（Group Relative Policy Optimization）
GRPO 是 DPO 的**分组扩展**，支持一组内多个有序回答：
\(Y = \{y_1 \succ y_2 \succ \dots \succ y_k\}\)

### 损失函数（**最小化**）
\[
\mathcal{L}_{\text{GRPO}} = -\mathbb{E}_{x,Y}\left[ \frac{1}{|Y|}\sum_{y_i\in Y}\log\left( \frac{\exp\big(\beta\cdot\Delta\pi(y_i)\big)}{\sum_{y_j\in Y}\exp\big(\beta\cdot\Delta\pi(y_j)\big)} \right) \right]
\]

**面试口述**：
在**同一组回答内部**做 softmax 归一化，让排名越高的回答，在组内的相对优势越大。

---

# 4. 面试一句话总结
- DPO：**二元偏好**，sigmoid 对比优劣对；
- GRPO：**分组多偏好**，组内 softmax 排序学习；
- 两者都**不训奖励模型**，直接对齐，更稳定、更好训。
