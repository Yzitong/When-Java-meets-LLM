# DeepseekMOE与MLA

在学习DeepseekV3之前，理解其核心组件——**MOE（Mixture of Experts，专家混合模型）**和**MLA（Multi-head Latent Attention，多头潜在注意力）**——是至关重要的。这两个组件是Deepseek系列模型架构中的关键创新，分别在DeepseekV1和DeepseekV2中提出并不断优化。本文将详细介绍MOE和KV Cache的概念，DeepseekMOE在MOE上的主要改进，以及MLA对传统多头注意力（MHA）的创新。文中将包含原理细节和相关公式，以帮助读者深入理解。

b站指路：

[DeepSeek-MOE原理讲解_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1uUPieDEK1?spm_id_from=333.788.videopod.sections&vd_source=aa6afb9d0536d09ecdcb5d2c1fcf4c79)

[deepseek 全网最硬核解读(五) Moe详解_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1riQpYMEUe?spm_id_from=333.788.videopod.sections&vd_source=aa6afb9d0536d09ecdcb5d2c1fcf4c79)

[DeepSeek-v2 MLA 原理讲解_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1BYXRYWEMj?spm_id_from=333.788.videopod.sections&vd_source=aa6afb9d0536d09ecdcb5d2c1fcf4c79)

![image-20250716135748448](https://raw.githubusercontent.com/Yzitong/LLM-Mastery-Journey/main/images/image-20250716135748448.png)

---

## 1. MOE（Mixture of Experts）与KV Cache

### 1.1 MOE（混合专家模型）

MOE是一种先进的神经网络架构，旨在通过将任务分解给多个“专家”来提高模型的性能和效率。每个专家是一个小型的子网络，专注于处理特定类型的数据或任务。MOE的核心在于使用一个“门控”机制来动态选择哪些专家处理当前的输入，从而在不显著增加计算成本的情况下扩展模型的参数数量。MOE架构相对于稠密网络，其训练的成本和推理的成本都更低。

<img src="https://raw.githubusercontent.com/Yzitong/LLM-Mastery-Journey/main/images/image-20250716180058035.png" alt="image-20250716180058035" style="zoom:80%;" />

#### 1.1.1 MOE的门控机制

门控机制通常通过一个门控网络（Gating Network）实现，它为每个专家分配一个权重，决定该专家对当前输入的贡献。给定输入 $x$，门控网络输出一个概率分布 $g(x)$，其中 $g_i(x)$ 表示第 $i$ 个专家的激活概率。

门控权重的计算通常使用softmax函数：

$$
g_i(x) = \frac{\exp(w_i \cdot x)}{\sum_{j=1}^{N} \exp(w_j \cdot x)}
$$

其中：
- $N$ 是专家的数量。
- $w_i$ 是第 $i$ 个专家的门控权重向量。

在MOE中，通常只选择top-k个专家（即门控权重最高的k个专家）来处理输入，以实现**稀疏激活**，从而控制计算成本。最终的输出 $y$ 是选定专家输出的加权和：

$$
y = \sum_{i \in \text{top-k}} g_i(x) \cdot \text{expert}_i(x)
$$

#### 1.1.2 MOE的优势与挑战

- **优势**：MOE允许模型在参数量大幅增加的同时，保持较低的计算成本，因为每次前向传播只激活一小部分专家。
- **挑战**：门控网络的训练较为复杂，容易出现专家选择不稳定或负载不均衡的问题（某些专家被过度使用，其他专家闲置）。

<img src="https://raw.githubusercontent.com/Yzitong/LLM-Mastery-Journey/main/images/image-20250716183817334.png" alt="image-20250716183817334" style="zoom: 50%;" />

为解决负载不均衡问题，通常在损失函数中加入负载均衡项：

$$
L_{\text{balance}} = \alpha \cdot \text{CV}(\{f_i\}_{i=1}^N)
$$

其中：
- $f_i$ 是第 $i$ 个专家被选择的频率。
- $\text{CV}$ 是变异系数，用于衡量专家选择分布的均匀性。
- $\alpha$ 是超参数，控制负载均衡的强度。

### 1.2 KV Cache（Key-Value缓存）

在Transformer模型中，KV Cache是一种用于优化推理效率的技术，尤其在自回归生成任务中表现突出。它通过缓存注意力机制中的键（Key）和值（Value）矩阵，避免在生成每个新token时重复计算先前的键和值。

没有KV Cache前每个token的MHA计算流程如下↓

![image-20250716190646686](https://raw.githubusercontent.com/Yzitong/LLM-Mastery-Journey/main/images/image-20250716190646686.png)

![image-20250716190525620](https://raw.githubusercontent.com/Yzitong/LLM-Mastery-Journey/main/images/image-20250716190525620.png)

有了KV Cache后计算每个token的MHA过程如下↓

![image-20250716191057834](https://raw.githubusercontent.com/Yzitong/LLM-Mastery-Journey/main/images/image-20250716191057834.png)

#### 1.2.1 标准多头注意力（MHA）中的KV Cache

在MHA中，注意力计算依赖于查询（Query）、键（Key）和值（Value）矩阵。给定输入序列 $X = [x_1, x_2, \dots, x_L]$，MHA的注意力输出为：

$$
\text{Attention}(Q, K, V) = \text{softmax}\left( \frac{Q K^T}{\sqrt{d_k}} \right) V
$$

其中：
- $Q = W_q X$，$K = W_k X$，$V = W_v X$。
- $d_k$ 是键向量的维度。

在自回归生成中，模型逐个生成token，每次生成新token时需要计算当前token与所有先前token的注意力。为避免重复计算先前的 $K$ 和 $V$，KV Cache将这些矩阵存储起来。对于序列长度 $L$，KV Cache存储：

- $K \in \mathbb{R}^{L \times d_k}$
- $V \in \mathbb{R}^{L \times d_v}$

每次生成新token时：
1. 计算新token的 $k$ 和 $v$。
2. 将其追加到KV Cache中。
3. 使用更新后的KV Cache计算注意力。

这将推理时间复杂度从 $O(L^2)$ 降为 $O(L)$，显著提升效率。

#### 1.2.2 KV Cache的内存挑战

尽管KV Cache优化了计算，但其内存占用随序列长度 $L$ 线性增长：

$$
\text{KV Cache size} = 2 \times L \times \text{numHeads} \times d_k
$$

对于长序列（如 $L = 128K$），内存需求可能成为瓶颈，限制模型处理长上下文的能力。

---

## 2. DeepseekMOE：让专家变得更加专精

DeepseekMOE是Deepseek系列模型中提出的MOE实现，首次在DeepseekV1中提出。它通过改进专家的选择和激活机制，提升了模型的性能和效率。DeepseekMOE引入了细粒度专家分割和共享专家隔离等创新，显著优化了传统MOE的局限性。

### 2.1 传统MOE的局限

在传统MOE中，专家数量较少且每个专家较大，可能导致专家学习到重叠的知识，造成参数冗余和计算资源浪费。

### 2.2 DeepseekMOE的改进

#### 2.2.1 细粒度专家分割

DeepseekMOE增加了专家数量 $N$，但减小了每个专家的参数规模，使其更专注于特定任务或数据子集。这种细粒度分割提高了专家的专业化程度，减少了知识冗余。

例如，传统MOE可能有8个专家，每个专家参数量为总参数的1/8；DeepseekMOE可能将专家数量增至64个，每个专家参数量降至总参数的1/64。这种设计使得模型在保持计算效率的同时，拥有更大的参数容量。

总有一些共有的能力所有的专家网络都需要，可以提取出来，作为共享专家。

![image-20250716184348232](https://raw.githubusercontent.com/Yzitong/LLM-Mastery-Journey/main/images/image-20250716184348232.png)

#### 2.2.2 共享专家隔离

DeepseekMOE引入了“共享专家”和“路由专家”的概念：
- **共享专家**：始终激活，负责处理通用特征或任务。
- **路由专家**：根据门控网络动态选择，处理专业化特征或任务。

最终输出 $y$ 结合了两类专家的贡献：

$$
y = \sum_{i \in \text{shared}} w_i \cdot \text{expert}_i(x) + \sum_{j \in \text{top-k}} g_j(x) \cdot \text{expert}_j(x)
$$

其中：
- $w_i$ 是共享专家的固定或可学习权重。
- $g_j(x)$ 是路由专家的门控概率。

这种设计确保共享专家处理基础特征，而路由专家专注于差异化知识，从而减少专家间的冗余。

#### 2.2.3 训练优化

为进一步提升专家的专业化，DeepseekMOE可能在训练中加入正则化项，鼓励共享专家和路由专家学习不同的表示：

$$
L_{\text{sep}} = \beta \cdot \sum_{i \in \text{shared}, j \in \text{routing}} \|\text{expert}_i(x) - \text{expert}_j(x)\|^2
$$

这有助于确保两类专家的功能互补，提升模型整体性能。

#### 2.2.4 Performance 对比

大大降低了激活参数的同时，效果更好。

![image-20250716150652881](https://raw.githubusercontent.com/Yzitong/LLM-Mastery-Journey/main/images/image-20250716150652881.png)

---

## 3. MLA（Multi-head Latent Attention）：MHA的创新改进

MLA是DeepseekV2中引入的一种新型注意力机制，旨在通过引入潜在空间来压缩KV Cache，解决传统MHA在长序列中的内存瓶颈问题，同时提升模型的效率和性能。

### 3.1 MHA的内存挑战

在标准MHA中，KV Cache的内存需求为 $O(L \times \text{numHeads} \times d_k)$，随着序列长度 $L$ 的增加，内存占用迅速成为限制模型处理长上下文的主要因素。

### 3.2 MLA的压缩机制

MLA通过将键（Key）和值（Value）向量投影到一个低维潜在空间来压缩KV Cache。具体来说，MLA使用低秩联合压缩技术，将每个token的键和值向量压缩为维度为 $d_c$ 的潜在向量，其中 $d_c \ll d_k \times \text{numHeads}$。

![image-20250716194443483](https://raw.githubusercontent.com/Yzitong/LLM-Mastery-Journey/main/images/image-20250716194443483.png)

#### 3.2.1 投影公式

对于原始键向量 $k \in \mathbb{R}^{d_k}$ 和值向量 $v \in \mathbb{R}^{d_v}$，MLA将其投影到潜在空间：

$$
k_{\text{latent}} = W_k \cdot k, \quad v_{\text{latent}} = W_v \cdot v
$$

其中：
- $W_k \in \mathbb{R}^{d_c \times d_k}$
- $W_v \in \mathbb{R}^{d_c \times d_v}$

类似地，查询向量 $q$ 也被投影：

$$
q_{\text{latent}} = W_q \cdot q, \quad W_q \in \mathbb{R}^{d_c \times d_q}
$$

![image-20250721101820268](https://raw.githubusercontent.com/Yzitong/LLM-Mastery-Journey/main/images/image-20250721101820268.png)

#### 3.2.2 注意力计算

在潜在空间中，注意力计算为：

$$
\text{Attention} = \text{softmax}\left( \frac{q_{\text{latent}} \cdot k_{\text{latent}}^T}{\sqrt{d_c}} \right) \cdot v_{\text{latent}}
$$

这种压缩显著减少了KV Cache的内存需求：

$$
\text{KV Cache size} = 2 \times L \times d_c
$$

由于 $d_c$ 远小于 $d_k \times \text{numHeads}$，内存占用大幅降低（据报道可减少93%）。

注：KV Cache 可以进行推理加速的原因，就是之前的token不用再计算一遍K、V，从而计算Attention分数更快。可是换到MLA之后不是又要重新计算K、V了嘛？

答：其实不然，根据下图的公式可以看出，虽然之前token的K、V多了一步计算。但我们目的是Attetion机制，从最后的推导公式来算，我们根本不需要算K、V。直接从Cache中拿出C就可以算出注意力机制分数。

![image-20250721101854613](https://raw.githubusercontent.com/Yzitong/LLM-Mastery-Journey/main/images/image-20250721101854613.png)

### 3.3 RoPE 改动：解耦 Q/K 位置编码 + 引入位置权重矩阵

为在压缩的潜在空间中有效捕捉位置信息，MLA引入了“解耦的旋转位置嵌入（Decoupled Rotary Position Embedding）”。这种位置编码方式通过旋转矩阵 $R(\theta)$ 调整 $q_{\text{latent}}$ 和 $k_{\text{latent}}$：

$$
q_{\text{latent}}' = R(\theta_q) \cdot q_{\text{latent}}, \quad k_{\text{latent}}' = R(\theta_k) \cdot k_{\text{latent}}
$$

这确保了模型在处理长序列时仍能准确感知token间的位置关系。

![image-20250721143709136](https://raw.githubusercontent.com/Yzitong/LLM-Mastery-Journey/main/images/image-20250721143709136.png)

传统 RoPE 通过**位置相关的旋转矩阵**，将位置信息嵌入 Q 和 K，使点积自然编码相对位置。但在 MLA 中，**低秩压缩与传统 RoPE 的 “位置依赖” 特性冲突**，因此需要改动：

#### 1. 传统 RoPE 与 MLA 的冲突

- **KV 是静态缓存**：K 来自压缩后的隐向量（推理时复用），其位置编码需预先处理；
- **Q 是动态生成**：每个新 token 的 Q 需实时计算，位置编码需动态更新；
- **旋转矩阵的位置依赖性**：传统 RoPE 的旋转矩阵随位置变化，导致**压缩矩阵（如 $W_{UR}$）无法与旋转矩阵融合**（固定矩阵 vs 动态旋转），破坏 MLA 的效率优势。

#### 2. 解耦 Q/K 位置编码的原因

| **维度**         | Query（Q）                               | Key（K）                             |
| ---------------- | ---------------------------------------- | ------------------------------------ |
| **计算时机**     | 动态生成（每个新 token 实时计算）        | 静态缓存（推理时复用历史隐向量）     |
| **位置编码需求** | 需灵活适配新位置（动态性）               | 需预先固定（缓存阶段确定位置）       |
| **设计逻辑**     | 基于隐向量**独立生成位置编码**（保表达） | 共享位置编码（降显存，牺牲少量表达） |

解耦后，Q 和 K 的位置编码流程分离，避免相互干扰，同时适配 “动态 Q + 静态 K” 的计算模式。

#### 3.引入位置权重矩阵的原因

引入位置权重矩阵（如 $W_{QR}$、$W_{KR}$）的原因可以从以下两方面解释：

- #### 矩阵融合优化

传统 RoPE 的旋转矩阵因随位置变化，无法与压缩/升维矩阵（如 $W_{UK}$）提前合并计算。引入 $W_{QR}$ 和 $W_{KR}$ 后：
- $W_{QR}$：生成查询 $Q$ 的**原始位置特征**，即 $Q = X W_{QR}$，再应用 RoPE 旋转（如 $Q' = \text{RoPE}(Q, \theta)$），动态捕捉位置信息。
- $W_{KR}$：生成键 $K$ 的**共享位置特征**，即 $K = X W_{KR}$，再复制给多个头，静态缓存以减少重复计算。
通过矩阵乘法结合律，固定矩阵（如 $W_{UK}$、$W_{UV}$）可提前融合为单一矩阵（如 $W' = W_{UK} W_{UV}$），推理时仅需一次矩阵乘法，降低计算量，保持 MLA 的效率。

- #### 2. 表达与显存的权衡

  - **Q 不共享位置编码**：每个头的 $Q$ 通过独立的 $W_{QR,i}$ 生成（如 $Q_i = X W_{QR,i}$），保留多头的位置表达能力。

  - **K 共享位置编码**：通过单一 $W_{KR}$ 生成共享特征（如 $K = X W_{KR}$），复制给多头，减少显存占用，但略牺牲表达能力。

### 3.4 重建损失

为保证压缩后的潜在表示保留原始向量的关键信息，训练时可能加入重建损失：

$$
L_{\text{recon}} = \| k - W_k^T \cdot k_{\text{latent}} \|^2 + \| v - W_v^T \cdot v_{\text{latent}} \|^2
$$

这有助于模型学习到高质量的压缩表示。

### 3.5 Performance对比

注意力机制的进化逻辑（MHA→GQA→MQA→MLA）

- **MHA（Multi-Head Attention）**：经典多头注意力，每个头独立计算 K/V，建模能力强但 KV 缓存大。
- **GQA（Grouped Query Attention）**：分组查询，头分成组（如 8 组），每组共享 K/V，平衡性能和显存。
- **MQA（Multi-Query Attention）**：多查询注意力，所有头共享 K/V，显存最优但性能最弱。
- **MLA（Multi-Head Latent Attention）**：DeepSeek 提出的改进版，通过**低秩压缩 K/V + 解耦位置编码**，同时优化显存和性能。

MoE 本身是 “动态激活专家”，MLA 进一步优化注意力的 **显存和计算效率**，两者结合让大模型在长序列和大参数量下更高效（如 Large MoE+MLA 的 KV 缓存仅 34K，却能支撑 247B 总参数）。

- **对用户**：让大模型能处理更长文本（如 128K 上下文），同时推理更快、显存占用更低。
- **对架构**：证明 “压缩注意力” 可同时实现 **显存↓、性能↑、效率↑**，打破 “显存和性能不可兼得” 的传统认知。
- **对 DeepSeek 迭代**：V2 通过 MLA 优化注意力，为 V3 的 671B 大模型（动态激活 37B 参数）奠定了 **高效推理的基础**—— 毕竟，再强大的模型，也要能 “跑起来” 才有价值。

![image-20250716145200113](https://raw.githubusercontent.com/Yzitong/LLM-Mastery-Journey/main/images/image-20250716145200113.png)

![image-20250716145329098](https://raw.githubusercontent.com/Yzitong/LLM-Mastery-Journey/main/images/image-20250716145329098.png)

---

## 4. 总结

- **MOE**：通过专家混合和稀疏激活实现高效的参数扩展，核心在于门控机制 $g_i(x) = \frac{\exp(w_i \cdot x)}{\sum_j \exp(w_j \cdot x)}$。
- **KV Cache**：优化Transformer推理，存储键和值矩阵，内存需求 $2 \times L \times \text{numHeads} \times d_k$。
- **DeepseekMOE**：在DeepseekV1中提出，通过细粒度专家分割和共享专家隔离提升MOE效率，输出 $y = \sum_{\text{shared}} w_i \cdot \text{expert}_i + \sum_{\text{top-k}} g_j \cdot \text{expert}_j$。
- **MLA**：在DeepseekV2中提出，通过潜在空间压缩KV Cache，注意力计算在低维空间进行 $\text{softmax}\left( \frac{q_{\text{latent}} \cdot k_{\text{latent}}^T}{\sqrt{d_c}} \right) \cdot v_{\text{latent}}$，显著降低内存占用。

DeepseekMOE和MLA的结合为DeepseekV3提供了强大的架构基础，使其在处理大规模数据和长上下文任务时更具优势。理解这些组件的原理和公式，有助于深入掌握Deepseek系列模型的创新之处。

### Deepseek MOE 主要意义：

通过 **动态激活专精化的专家子网络**，让大模型从 “大而全的冗余架构” 转向 “多专家分工协作”，在维持百亿级总参数规模的同时，仅激活少量核心参数完成计算（如 DeepSeek-V3 仅激活 37B 参数），既降低训练 / 推理成本，又支持通过提示词引导 “专家角色”（如指定数学 / 代码专家）提升任务针对性，实现 **“大参数规模” 与 “高效计算” 的平衡**。

### Deepseek MLA 主要意义：

通过 **低秩压缩键值对（KV）+ 解耦位置编码**，将传统多头注意力（MHA）的 KV 缓存占用降低 85%~96%（如长序列下从 860K 降至 34K），同时保留甚至提升模型性能（如 Large MoE+MLA 在 MMLU 任务上准确率提升 1.5%），**突破长文本推理的显存瓶颈**，让大模型高效处理 128K 级超长序列成为可能。