# DeepSeek Super Agent（超级智能体）

- 发布：2026-10-31，DeepSeek Harness 团队《探索超级智能体设计：迈向更自主的 Agent 系统》
  （微信长文，Qiao Yujia 执笔）；同日开源 `dsh-superagent-prototype`
  （MIT，研究原型，非官方产品）
- 定位：一个 7×24 在线、跨平台共享上下文、并行处理复杂任务并主动行动的**自主协作者**
- 口号：*"take as little human attention as possible, and give as much value as possible"*

## 核心判断

单任务 Agent（尤其 coding agent）已证明模型能用工具、少人监督地解决复杂问题；
人的角色上移到**决策与验收**。此时生产力的瓶颈从模型能力转向**人的注意力**：
一个人通常只能同时跟进 **3–5 个活跃 Session**。单任务的上限由模型能力决定，
多任务的上限由人的注意力决定。

Super-agent 要打破 Agent 与"人工发起的单个 session"的绑定，成为**跨会话保持上下文、
并行推进多任务且互不干扰的连续实体**。

## 四个基本特征

1. **连续（Continuity）**：用户在 IM、GitHub 评论等熟悉界面直接交互，感觉始终只有
   一个 Agent，不需要想"Session X 的上下文里有没有 Y"
2. **高效（Efficiency）**：快速响应 + 并行完成多个复杂任务，前一个任务未完即可接受
   下一条指令（全双工）
3. **主动（Proactivity）**：持续观察环境事件，对值得处理的事采取行动；无需干预时保持沉默
4. **持久（Persistence）**：始终在线，直连真实工作环境，长时运行中自动从异常恢复

## 五大模块

### 1. 双层架构（连续性与吞吐的平衡）

- **Router 层**：持久全局路由层，处理所有入口的人机交互 + 任务分派；是全局顺序点、
  协调点和记忆。**自己不执行具体任务**（"甚至可能没有 Bash 工具"），可用较轻的模型。
  实现上是一个持久 LLM Session：事件在推理间隙累积、下一轮 batching 处理——
  积压越多，单次推理摊到每个决策的时间越少
- **Worker 层**：执行会话，上下文互相隔离，全套执行工具，聚焦单一任务；
  通常用最强模型。**不直接与人交互**，向 Router 汇报，由 Router 代为回复
- 规模扩展：Router 可多层过滤或水平分片（sharding），代价是跨分片记忆/连续性
  （全局记忆问题未解决前的固有 trade-off）

### 2. 事件驱动（主动性的来源）

- **Trigger**：环境中的半结构化事件——群聊消息、邮件、日历提醒、代码评审评论、CI 结果
- **事件过滤**：策略（Policy）准入，非允许来源/非准入类别的候选被忽略
- **事件路由**：订阅（subscription）+ 直达投递；线程与工单的订阅关系决定消息流向
- **上下文组织**：Chain of Recall——按需召回相关历史，而非全量塞入
- **Acting in Advance（投机执行）**：借鉴 speculative execution，在不确定结果会被使用
  之前先后台行动（收到会议邀请 → 后台查日历冲突、收集背景、起草回复），有价值才交付，
  否则丢弃。内部实践：群内"静默复现并修复"策略——有人报问题时后台复现并找修复，
  确认问题且有简单修复才交付，否则全程沉默

### 3. 以人为中心的交互

- **全双工**：异步 ReAct Loop——用 `start()`/`stop()` 替代同步 `run()`，调用立即返回
  "Started"，后台完成后再作为新事件消息投递回 session。前台对话不被慢工具阻塞
- **分通道适配**：
  - IM 通道：轻打扰，像人类同事一样只在需要决策/验收时通知；硬规则：每条回复 ≤100 词
  - GitHub/邮件/文档等异步通道：高密度结构化交付（PR 描述、报告）

### 4. 计算、状态、环境三分离

- Agent Loop 部署在云端，执行设备（本地机器/设备）装轻量 daemon
- 状态独立持久化，跨设备、跨重启存活
- 开放问题：云端 Loop 被攻破（入侵/prompt injection）可能连带所有设备；
  凭据不得进入模型上下文（需要"用而不碰"的机制）

### 5. Watchdog（外部控制回路）

- 独立于主系统的 **Watchdog Session**，由定时消息驱动
- 不仅监控进程存活，更用模型判断 **Worker 是否在实质推进、是否偏离总目标**
  （Agent 的失败常常不是崩溃，而是陷入无产出的循环或目标漂移）
- 检测到异常即介入修复；对间歇性外部故障持续检查直到恢复
- Watchdog 本身是带全套工具的 Agent Session，维护者可用自然语言询问故障、
  甚至在线修改主系统行为（增删 prompt、调整 trigger 规则）

## 实验（初步）

- 单会话方案 p95 任务完成时间 29.5 min → Super-agent 双层架构 **15.7 min**（并行 Worker）
- 代价：缓存读取 token 约 600K → 2.3M（用后台计算/上下文开销换更平稳的并行能力；
  缓存 token 不等于实际费用同比例增长）
- Router 层 batching 消融：事件积压时多 Trigger 合并进一次 LLM 调用，
  prefill 并行，单次请求时延随积压量增长远慢于线性
- 注意：实验验证的是**调度/吞吐**，不是产物质量或组织级收益

## 内部实例（已在运行）

- 同一开发任务在 **IM 群聊 ↔ GitHub PR 评审**之间连续推进
- 持续监控公共群聊，**无人 @ 时主动识别缺陷、定位根因、提交修复 PR**
- 日常参与问题复现与修复、代码审查响应、DeepSeek Harness 自身的迭代

## 对本部署的意义

当前部署（dsh-superagent on DeepSeek Harness）运行的就是这套架构：
Router（orchestrator）评估/路由候选、Worker（ticket session）执行、
Trigger 来自 GitHub Source、Watchdog 由 Harness 提供。
做 TV personal agent 时，这套"运营骨架"可直接复用——见
[06-tv-personal-agent.md](06-tv-personal-agent.md)。

## 参考

- 设计全文（英文）：<https://offloop.org/learn/deepseek-super-agent-design>
- 中文原文（知乎）：<https://zhuanlan.zhihu.com/p/2099987320443237060>
- 原型仓库：<https://github.com/deepseek-ai/dsh-superagent-prototype>
- SCMP：<https://www.scmp.com/tech/article/3372884/deepseek-unveils-ai-super-agent-coding-race-openai-anthropic>
- Help Net Security：<https://www.helpnetsecurity.com/2026/11/02/deepseek-dsh-superagent>
- 腾讯新闻：<https://view.inews.qq.com/a/20261102A02UR800>
- 新浪：<https://finance.sina.com.cn/tech/csj/2026-11-01/doc-inixepty4335109.shtml>
- 第三方解读：<https://cellcog.ai/blog/deepseek-super-agent> · <https://kingy.ai/blog/deepseek-super-agent-design-explained>
