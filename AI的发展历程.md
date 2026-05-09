# 1 LLM技术基础
2017 Attention is All You Need 提出自注意力机制、Transformer架构，奠定大语言模型的技术基础
编码器类型：
一一一

| 架构流派                | 代表模型                    | 核心机制               | 主要优势                                                                                                                                                                               |
| ------------------- | ----------------------- | ------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Encoder-Only**    | BERT, RoBERTa, ALBERT   | 双向自注意力机制           | **深度理解**: 能同时看到单词的上下文，捕捉丰富的语义信息[](https://developer.baidu.com/article/details/3321335)[](https://cloud.baidu.com/article/5595222)。                                                 |
| **Encoder-Decoder** | T5, BART, 原始Transformer | 编码器压缩输入，解码器基于此生成输出 | **序列转换**: 擅长将一个序列映射到另一个不同长度的序列，灵活度高[](https://cloud.tencent.com.cn/developer/article/2398327?from=15425)[](https://developer.baidu.com/article/details/3321335)。                   |
| **Decoder-Only**    | GPT系列, LLaMA, Mistral   | 单向（因果）自注意力机制       | **文本生成**: 擅长自回归地预测下一个词，生成流畅、连贯的文本[](https://developer.baidu.com/article/details/3321335)[](https://ones.com.cn/tech-news/comprehensive-guide-to-llm-architectures-evolution-2024)。 |
 ![[Pasted image 20260508185922.png]]
# 2 ChatBot 
与大语言模型进行交互的初步形式
## 2.1 Prompt Engineering
提示词（Prompt）即引导模型按照特定意图生成输出的输入指令。主要包含系统提示词和用户提示词。
提示词工程通过设计和优化提示词，使大模型更加准确、可控的产生所需输出，低成本调优：提升效果但不改变模型参数。
https://www.aneasystone.com/archives/2024/01/prompt-engineering-notes.html
### 2.1.1 Zero-shot Prompting VS Few-shot Prompting

### 2.1.2 COT
COT通过向大模型展示中间推理步骤以实现复杂的推理能力，结合少样本提示还可以获得更好的结果。
### 2.1.3 RAG
检索增强生成先从外部知识库检索相关信息，再结合这些信息一起生成回答，从而提升模型的准确性和知识的时效性。
ps.未真正改变模型参数，提升效果有限。
![[Pasted image 20260508193441.png]]
## 2.2 Fine-tuning 
微调是在已有模型基础上，用特定数据再训练，让模型更适合某个具体任务或场景。
微调要训练的是模型的参数。LoRA算法通过只训练少量低秩参数来进行微调，大幅降低了训练成本。
ps.可能垂直领域微调不及基座模型更新见效更快

# 3 Agent
Agent 是一种能够基于目标进行“思考-行动-观察”循环、能够自主地调用工具来完成复杂任务的智能系统。
ps.提示词+LLM+Tools 即可构成一个最简单的Agent。
AI Agent的四代发展历程：

| 第X代 | Agent架构                         | 缺陷                                                                                  | 应用范式                                                                                                                              | 时间线   |
| --- | ------------------------------- | ----------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- | ----- |
| 0   | ChatBot <br>eg.chatgpt          | 1. 数据滞后+幻觉：知识截止到训练截止日；编造答案<br>2. 没有行动臂：能力限制在聊天框中                                    | 1. PE：zero-shot/few-shot、rag                                                                                                      | 22年末  |
| 1   | 初步四肢+ 任务拆解eg.autogpt            | 1. 任务迷失：在错误的推理分支越走越远，盲目行动<br>2. 燃烧账单                                                | 1. Function Call<br>2. 目标-拆解-执行-评估-循环                                                                                             | 23年6月 |
| 2   | 工程化架构<br>eg.ReAct框架             | 1. 多agent协作产生噪声<br>2. 任务复杂但不解决问题                                                    | 1. ReAct：思考-行动-观察<br>2. 吴恩达设计模式：反思-工具调用-规划-多智能体协作                                                                                 | 23年末  |
| 3   | 标准化协议+行动空间革命<br>eg.Coding Agent | 1. 代码能跑 not架构健康<br>2. 功能可用 not边界可靠<br>3. 增加更多理解需求、矫正边界、维护架构等工作，不一定提升效率，需评估、审查、测试、回滚 | 1. MCP：解决工具连接问题：统一方式<br>2. Computer use:从API调用到操作真实图形界面，解决无API的工具调用问题。<br>3. Coding Agent：Gui:cursor 深度IDE分支；CLI：Claude Code 终端调用 | 24年4月 |
| 4   | 技能封装+常驻自治<br>eg.OpenClaw        | 1. 安全、权限问题                                                                          | 1. skill：将专业方法封装成一个可复用、agent可读取的程序化的知识<br>2. 渐进式披露：组成分层知识体系：技能索引、skill完整说明、脚本/模版<br>3. Heartbeat：周期性醒来                            | 25年末  |
| ？   |                                 |                                                                                     |                                                                                                                                   |       |


设计模式：
- https://tw93.fun/2026-03-21/agent.html
- https://mp.weixin.qq.com/s/7CZ6cHWQ-T9bmaWoJFwdwA
## 3.1 Context Engineering
上下文指的是Agent运行中需要提供给LLM的一切相关信息（如历史对话、用户输入、背景知识、工具结果等）。
ps.上下文工程关注如何高质量筛选、压缩和组织上下文，放入下一轮窗口，从而最大化模型决策和推理能力。
进行上下文工程的原因：
- 上下文窗口有限，需压缩和裁剪
- 冗余/有毒的上下文影响注意力/决策
https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents
https://mp.weixin.qq.com/s/KbviOJ6q-K4ik_wzsUs2dw?open_in_browser=true
## 3.2 Muti Agent
单个Agent缺点：
- 上下文窗口有限
- 内容太多/太杂导致注意力分散/稀释

使用多Agent更优秀的情况
- 单Agent上下文污染
- 多Agent并行运行会提速
- 拆分多个子Agent会改善工具决策效果，内容更聚焦

ps.子Agent的拆分应该按照不同的上下文进行拆分而非按照按照任务拆分。
## 3.3 Function Call
 Function Call（函数调用）在大语言模型（如GPT）的上下文中，指的是模型在生成回复时，不直接输出最终答案，而是输出一个结构化的指令（例如JSON），这个指令可以被程序解析并调用一个外部函数或API来获取信息或执行操作，然后再将结果返回给模型继续生成回复。
ps.让大模型按约定格式输出调用指令，从而由外部系统真正执行具体操作的一种机制。
## 3.4 MCP
MCP是一种标准化协议，用来让大模型以统一的方式连接外部工具、数据源和服务，从而获取上下文信息并执行操作。
ps.MCP最大贡献是使得工具可以跨AI应用复用，推动社区生态发展。
## 3.5 Agent Skill
Agent Skill 是将一整套Agent能力（Prompt、工具脚本、知识文件等）封装为可复用的模块。
ps.约等于一个子Agent，适合sop的沉淀和复用。
优点：
- 提示词的渐进性披露解决上下文爆炸问题
- 相较于MCP可复用工具，直接复用子Agent
- https://claude.com/blog/equipping-agents-for-the-real-world-with-agent-skills
- https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview
- https://agentskills.io/home
## 3.6 Harness Engineering
驾驭工程强调通过构建受控环境，让Agent在约束下高效可靠地完成长周期复杂任务，包含围绕Agent构建约束机制、反馈回路、可靠上下文等等一系列工程实践。


参考文档：
1. b站up主大模型发展总结：https://oigi8odzc5w.feishu.cn/wiki/WBMfwiNkfi6uNFkRtXdcavDzn0e?from=from_copylink
2. b站up主AI Agent编年史：https://www.bilibili.com/video/BV1NL9tBsELS/?spm_id_from=333.1391.0.0&vd_source=eb8b7c6e7469cdcb996a41649e1220b0

todo
1. 大模型理论：https://www.bilibili.com/video/BV19MF5zVEwy/?spm_id_from=333.337.search-card.all.click&vd_source=eb8b7c6e7469cdcb996a41649e1220b0（毛玉仁）
2. Agent学习&实践：https://github.com/datawhalechina/hello-agents（实践一下大模型chatbot应用的整个流程：PE（Few-Shot/COT/Rag)-微调；实践一下agent/workflow应用的整个流程：架构设计/评测）
3. 解构下claude code/openclaw架构
4. CV/NLP：「CS231N」深度学习CV（前置课程可以看「CS229」机器学习基础课/小土堆/吴恩达/李宏毅/cs3b1b/)、「CS224W」图神经网络、「CS224N」深度学习NLP
5. 学习&实践embedding，记得实践下图神经网络
6. 机器学习/深度学习基础知识：https://www.huaxiaozhuan.com/
7. 全流程学习：https://csdiy.wiki