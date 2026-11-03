# Coworker（AI 数字同事）：产品格局与学术进展

## 概念区分

"AI agent" 是技术（会规划、会调工具的模型）；"AI coworker" 是**部署形态**——
住在团队/家庭的聊天里、连着工作工具、像同事一样被 @（Viktor 的定义，
<https://viktor.com/blog/what-is-an-ai-coworker>）。

另一个角度（eigent.ai）：AI coworker 是**多个专业化 Agent 的协调系统**，
像一支团队——各有角色、并行执行、共享上下文、自动交接。

## 产品格局

| 产品 | 要点 |
|---|---|
| [Coworker](https://coworker.ai) | 企业 AI workspace，**组织记忆**为底座；$13M seed（2025-05，Business Insider）；50+ 原生读写集成（CRM/通讯/支持/文档/代码/数据）；模型无关（Anthropic/OpenAI/Google/Moonshot/Z.ai），新模型自动接入；MCP 输出上下文 |
| [Viktor](https://fortune.com/2026/05/19/viktor-ai-startup-raises-75-million-for-virtual-coworker-exclusive) | Slack/Teams 里的虚拟同事；$75M（2026-05，Fortune 独家）；Meta 系团队 |
| Salesforce Agentforce Coworker | Data 360 驱动的企业自主队友，"停配置、开始执行" |
| Google Cloud Gemini "Coworker Agent" | Agent 拥有**独立企业邮箱、专属存储空间、公司目录身份**，参与邮件/文档协作；以团队成员的工作身份承担**持续性任务**（周期性资料整理、信息汇总、邮件准备），不等员工开聊天窗口 |
| AWS "Digital Employee" | 数字员工定义：AI 驱动、自主理解任务并交付成果的软件工作者 |

行业共识：**2026 = 数字员工元年**（腾讯云开发者社区等）。

## 学术侧

- **[From Chatbot to Digital Colleague](https://arxiv.org/abs/2606.14502)**
  （arXiv 2606.14502，Tencent）：从"对话式回答"到"持续性工作"的范式转变。
  两条主线：① 认知核心——Chatbot 式快思考 → Thinking LLM（推理时计算、CoT、
  反思、过程监督、RL）；② 任务执行——临时 tool-calling Agent → **OpenClaw 式
  工作站**（持久 Workspace + Skill + 验证回路 + 治理）。"Workspace + Skill" 范式
  让 episodic 工具使用变得像同事：状态持久化、可复用流程、任务闭环、经验复用。
  数据构造从 instruction-response 对转向 **State-Action-Observation 轨迹**；
  评估从静态 benchmark 转向沙箱化、可审计、自进化生态
- **[Working with Agentic 'Teammates'](https://arxiv.org/html/2609.29901v1)**
  （arXiv 2609.29901）：组织行为研究——新的"组织成员"与人类工作生态的碰撞
  （2026 年 6–7 月半结构化访谈）
- MIT Technology Review（2026-04）：中国程序员用 **"Colleague Skill"** 训练 AI 分身——
  导入飞书/钉钉聊天记录与文件，自动生成可复用手册，让 Agent 复刻某位同事的职责
  与习惯（<https://www.technologyreview.com/2026/04/20/1136149/chinese-tech-workers-ai-colleagues>）

## 中国人民大学最新论文（coworker 相关）

- **EnvScaler**（2026-01，MIT 开源）：workplace agent 环境生成——先生成环境骨架，
  再生成场景与验证函数；191 个环境 + SFT/RL 数据 + 训练代码与模型
  （收录于 [awesome-workplace-agent-environments](https://github.com/gumlau/awesome-workplace-agent-environments)）
- **跨 harness 训练**（2026-10-21 arXiv，RUC + IQuest，Fei Bai、Huatong Song 等）：
  在 18 种 harness 上训练的 Agent 胜过单一 harness 训练
- **Arbor**（RUC + Microsoft Research）：把 AI 驱动的研究/优化从试错序列升级为
  树状**累积学习**，同算力预算下 2.5× 于 Claude Code/Codex
- **CCL 2026**：于兆洋（高瓴 24 级本科、MetaGPT 研究员、OpenManus 49K star）——
  受大脑启发的基础智能体架构（认知/记忆/感知/行动模块 + 自优化机制 + 多智能体协作）
- 高瓴学院成果：涉外法治大模型（与法学院，上万部域外法律法规知识库 + 多语言法条
  检索器 + 搜索智能体工作流）；"玉兰-万象"社会模拟平台 2.0（2026-08 发布）；
  文澜/玉兰系列大模型、玉兰-LLaDA 扩散语言模型

## 复旦大学最新论文（coworker 相关）

- **GenericAgent**（arXiv 2604.17091）：token 高效的自进化 LLM Agent——层级记忆 +
  上下文信息密度最大化
- **SciAgentGym**（ICML 2026，与上海 AI 实验室）：评估 Agent 多学科任务上的
  多跳科学工具使用能力（可执行、可反馈的 Agent 环境）
- **LEGALWORLD**（EMNLP 2026，数据智能与社会计算实验室）：首个面向中国民事诉讼
  全生命周期的法律智能体交互环境——五阶段七子场景连续状态链（法律咨询/文书起草/
  一审/上诉文书/二审）；局部记忆 + 全局案件记忆 + 模块化 Skill/Tool 库；
  LongJud-Bench 评估长程法律智能体
- **Thought-Aligner**（ICML 2026，上海创智学院 × 复旦）：Agent 安全新范式——
  从阻断式"规则拦截"到修复式"**思维校正**"：行动前先校正思路、修正危险推理，
  让智能体"三思而后行"，在继续完成任务的同时更审慎
- Dewen Liu（复旦）：LLM 多智能体系统科学评估框架（NeurIPS 2026 投稿，
  arXiv 2602.05289）——collaboration-gain 指标区分真实协作与资源堆叠
- **SocioVerse**：LLM-Agent 驱动的社会模拟世界模型（千万级真实用户池）

## 个人 Agent 评测基准（TV personal agent 可直接借用）

- **VibeLifeBench**（arXiv 2608.10875）：*Can Your Life Agent Be Proactive and
  Persistent in a Living World?*——旗舰任务：20 天家庭日本游，2026-04-17 至
  05-16、24 个阶段、三阶段（两周准备期等）
- **π-Bench**（arXiv 2605.14678）：proactive 个人助理在长程工作流中的评测
  （受 OpenClaw 生态影响：持久工作区/产物、多日 living-world 同事任务）
- **Claw-Anything**（arXiv 2605.26086）：always-on 个人助理基准——40+ 后端服务
  的持久状态库（生活/工作域）+ 3 个月以上系统级与服务级活动流，含无关事件干扰

## 参考

- 10 Best AI Coworkers in 2026：<https://www.vellum.ai/blog/best-ai-coworkers>
- AI Coworker vs AI Agent：<https://www.eigent.ai/blog/ai-coworker-vs-ai-agent>
- 36氪（Google Gemini Coworker Agent）：<https://m.36kr.com/p/4018047075045504>
- 钛媒体（人机协同三模式：Embedding/Copilot/Agent）：<https://www.tmtpost.com/6922846.html>
- CCL 2026 论坛：<https://cips-cl.org/static/CCL2026/cclProgram/forum/index.html>
- 复旦 5 月科研速递：<https://ciram.fudan.edu.cn/f0/86/c49211a782470/page.htm>
