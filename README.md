# 🧠 LLM Mastery Journey: 大模型学习笔记与实践

> "Learning is not attained by chance, it must be sought for with ardor and attended to with diligence." - Abigail Adams

这是一个记录我探索大语言模型(LLM)世界的个人学习仓库。在这里，我整理了从基础概念到前沿研究的学习笔记、实践项目和思考总结。无论你是AI领域的初学者，还是寻求深入理解大模型的研究者，都能从中找到有价值的内容。

<a href="https://github.com/yourusername/llm-mastery-journey/stargazers">
  <img src="https://img.shields.io/github/stars/yourusername/llm-mastery-journey?style=social" alt="GitHub Stars">
</a>
<a href="https://github.com/yourusername/llm-mastery-journey/network/members">
  <img src="https://img.shields.io/github/forks/yourusername/llm-mastery-journey?style=social" alt="GitHub Forks">
</a>
<a href="https://github.com/yourusername/llm-mastery-journey/issues">
  <img src="https://img.shields.io/github/issues/yourusername/llm-mastery-journey" alt="GitHub Issues">
</a>
<a href="https://github.com/yourusername/llm-mastery-journey/blob/main/LICENSE">
  <img src="https://img.shields.io/github/license/yourusername/llm-mastery-journey" alt="License">
</a>


## 🚀 仓库亮点

- **系统化学习路径**：从基础概念到前沿研究，构建完整知识体系
- **理论与实践结合**：包含代码实现、实验分析和应用案例
- **持续更新**：追踪大模型领域最新进展，及时补充笔记
- **社区互动**：欢迎提交PR、讨论问题、分享见解
- **视觉化呈现**：使用图表、流程图等直观展示复杂概念

> 💡 本项目文档包含 LaTeX 数学公式。  
> 如需在 GitHub 上直接浏览公式，推荐安装 [GitHub with MathJax 插件](https://chrome.google.com/webstore/detail/github-with-mathjax/ioemnmodlmafdkllaclgeombjnmnbima)。  
> 或点击 [在线版文档（支持公式渲染）](https://hackmd.io/@你的id/你的文档) 进行阅读。

## 📚 目录结构
LLM-Mastery-Journey/
├── 01-BERT与大模型组件/           # 第1周：基础组件攻坚
│   ├── 01-基础认知阶段/  
│   │   ├── 词向量与语义建模/  
│   │   │   ├── theory.md         # 稀疏/稠密词向量对比、溢出词处理  
│   │   │   ├── experiment.ipynb  # Gensim实现Word2Vec句子相似度  
│   │   │   ├── interview-qa.md   # 词向量建模逻辑、分词粒度影响  
│   │   ├── BERT输入层与位置编码/  
│   │   │   ├── theory.md         # 三嵌入解析、位置编码原理  
│   │   │   ├── code-snippet/     # 手动实现嵌入相加  
│   │   │   ├── visualization/    # 位置编码热力图对比  
│   ├── 02-深入原理阶段/  
│   │   ├── Transformer注意力机制/  
│   │   │   ├── math-derivation.md # 注意力公式推导（含√d_k作用）  
│   │   │   ├── from-scratch.ipynb # 从零实现多头注意力  
│   │   │   ├── debug-notes.md    # softmax数值不稳定等踩坑记录  
│   │   ├── 大模型组件对比（RoPE/ALiBi）/  
│   │   │   ├── comparative-experiment.ipynb # 长文本场景对比实验  
│   │   │   ├── performance-analysis.md # 训练速度与语义捕捉分析  
│   ├── 03-实战应用阶段/  
│   │   ├── BERT微调全流程/  
│   │   │   ├── end2end-tutorial.ipynb # Hugging Face微调文本分类  
│   │   │   ├── optimization-tips.md # 学习率、数据增强调优技巧  
│   │   ├── 大模型组件落地案例/  
│   │   │   ├── long-text-processing.md # Sparse Attention处理长文本  
│   │   │   ├── deployment-example/ # FastAPI部署示例（含Docker配置）

├── 02-Pre-training/              # 第2周：预训练全流程
│   ├── 01-data-prep/             # 数据预处理
│   │   ├── open-datasets.md       # 开源数据集调研（中英文混合）
│   │   ├── data-cleaning.ipynb    # 数据清洗与预处理流程
│   │   ├── tokenization-guide.md  # 分词/词元化指南（中文分词特殊处理）
│   │   ├── data-expansion.ipynb   # 数据扩展实践（回译、同义替换）
│   ├── 02-pretrain-core/          # 预训练核心
│   │   ├── pretrain-vs-finetune.md # 预训练与微调对比（含数学推导）
│   │   ├── pretrain-loss.ipynb    # 预训练损失函数实现（MLM、NSP）
│   │   ├── efficiency-optimization.md # 预训练效率优化（模型并行、激活重计算）
│   │   ├── stability-tricks.ipynb # 预训练稳定性保障（梯度裁剪、权重初始化）

├── 03-Post-training/             # 第3周：微调与对齐
│   ├── 01-alignment/              # 大模型对齐
│   │   ├── rlhf-principle.md      # RLHF原理详解（PPO、DPO）
│   │   ├── reward-model.ipynb     # 奖励模型训练实践
│   │   ├── ppo-training.md        # PPO训练流程与实现
│   │   ├── alignment-evaluation.ipynb # 对齐效果评估
│   ├── 02-parameter-efficient/    # 参数高效微调
│   │   ├── lora-theory.md         # LoRA原理与数学推导
│   │   ├── peft-comparison.ipynb  # PEFT方法对比（LoRA、QLoRA、Adapter）
│   │   ├── multi-task-finetune.md # 多任务微调策略
│   │   ├── vertical-finetune.ipynb # 垂直领域微调实践（通信领域）

├── 04-Applications/              # 第4周：大模型应用
│   ├── 01-RAG/                    # 检索增强生成
│   │   ├── rag-architecture.md    # RAG系统架构设计
│   │   ├── embedding-evaluation.ipynb # 嵌入模型评估与选择
│   │   ├── vector-db-practice.md  # 向量数据库实践（Chroma、Milvus）
│   │   ├── rag-evaluation.ipynb   # RAG系统效果评估
│   ├── 02-Agents/                 # 智能代理
│   │   ├── agent-principles.md    # Agent设计原则
│   │   ├── tool-integration.ipynb # 工具集成与调用
│   │   ├── multi-turn-conversation.md # 多轮对话管理
│   │   ├── agent-application-examples.md # Agent应用案例（智能客服、代码助手）

├── 05-Practice-Projects/          # 第5周~第6周：实践项目
│   ├── 01-communication-model/    # 通信领域大模型
│   │   ├── project-proposal.md    # 项目方案（LoRA+RAG构建通信模型）
│   │   ├── data-collection.md     # 通信数据采集（协议文档、故障案例）
│   │   ├── training-pipeline.ipynb # 训练全流程（LoRA微调+RAG集成）
│   │   ├── model-evaluation.md    # 模型评估报告
│   ├── 02-smart-customer-service/ # 智能客服Agent
│   │   ├── requirements-analysis.md # 需求分析（通信故障问答）
│   │   ├── dialog-design.ipynb    # 对话流程设计
│   │   ├── agent-training.md      # Agent训练与优化
│   │   ├── demo-implementation/   # 演示实现（Streamlit前端）

├── 06-Frontier-Reports/          # 第7周：技术报告阅读
│   ├── 01-model-survey/           # 前沿模型调研
│   │   ├── deepseek-analysis.md   # DeepSeek模型技术解析
│   │   ├── qwen-vs-llama.md       # Qwen与Llama模型对比
│   │   ├── baichuan-evaluation.md # 百川模型评估与应用场景
│   │   ├── gpt-evolution.md       # GPT系列演进分析
│   │   ├── cross-model-comparison.ipynb # 跨模型对比实验
│   ├── 02-research-paper/         # 前沿论文复现
│   │   ├── flash-attention-reproduce.ipynb # FlashAttention复现
│   │   ├── moe-implementation.md  # MoE模型实现与优化
│   │   ├── efficient-pretraining.md # 高效预训练方法研究
│   │   ├── research-trends.md     # 大模型研究趋势分析

├── 07-Interview-Prep/             # 第8周：大厂面经查缺补漏

├── resources/                     # 补充资源
│   ├── papers/                    # 经典论文
│   ├── cheatsheets/               # 知识速查表
│   ├── datasets/                  # 示例数据集（小型）
│   ├── tools/                     # 工具推荐与使用指南
├── README.md                      # 项目总指引
├── LICENSE.md                     # 开源协议
├── CONTRIBUTING.md                # 贡献指南 

## 🤝 如何参与

1. **贡献笔记**：如果你有关于大模型的学习笔记或见解，欢迎提交PR
2. **提出问题**：遇到疑问或发现错误，可在Issues中提出
3. **分享资源**：推荐优质学习资源，共同丰富知识库
4. **参与讨论**：加入仓库讨论，分享你的学习心得和经验

## 🌟 如何获取更多价值

1. **订阅仓库更新**：点击右上角"Watch"按钮，及时获取最新内容
2. **点赞支持**：如果你觉得内容有帮助，别忘了给仓库点个Star
3. **参与实践项目**：尝试完成仓库中的实践项目，加深理解
4. **关注作者**：在GitHub上关注我，获取更多相关内容

## 📜 许可协议

本仓库内容采用 [MIT License](https://github.com/yourusername/llm-mastery-journey/blob/main/LICENSE) 许可协议。你可以自由使用、修改和分享这些内容，但请保留原作者信息和许可声明。

## 📞 联系我

如果你有任何问题、建议或合作意向，欢迎通过以下方式联系我：

- GitHub: [@Yuzitong](https://github.com/Yuzitong)
- Email: yztong@bupt.edu.cn

感谢你的关注和支持！希望这个仓库能帮助你在大模型学习的道路上取得进步。

