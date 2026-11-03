# CoworkOS 论文调研 & 对比 DeepSeek Super Agent

## 论文信息

- **CoworkOS: Scaling Agent Coworkers for Real-world Automation**
- arXiv: [2611.03178](https://arxiv.org/abs/2611.03178)（2026-11，34 页，cs.AI/cs.CL，CC-BY-NC-SA 4.0）
- **团队**：中国人民大学 + 复旦大学为主，联合北大、上海交大、NUS、华东师大
  - 核心贡献：Jiajie Jin（人大）、Guanting Dong（人大）、Chengjun Pan（北大）、
    Xin Deng（复旦）、Zhuohui Sheng（复旦）、Guoqiang Wang、Tao Gui（复旦）、Yutao Zhu（人大）
  - 通讯作者：Tao Gui（复旦）、Yutao Zhu（人大）
  - 指导：Tao Gui、Yu-Gang Jiang（复旦）、See-Kiong Ng（NUS）、Ji-Rong Wen、Zhicheng Dou（人大）等
- **定位**：一个 **cowork harness**（长程 + human-in-the-loop 的 Agent 运行时）。
  核心问题：*一个 cowork agent 能否既处理复杂生产力任务，又做持续监控与决策，同时让人留在环里？*
- **分工原则**：*模型自主选任务执行策略，运行时 harness 负责高效的长程 cowork 机制*
- 论文把 Agent 系统演进分三阶段：Agent Workflow（CoT/ReAct/MetaGPT/DSPy/AFlow）→
  Agent Assistant（记忆/消息/代码执行：LangGraph/AutoGen/OpenClaw）→ **Agent Cowork**
  （人与 Agent 共同贡献于不断演化的工作产物；点名 Claude Code、Codex、OpenHands、
  **DeepSeek Harness** 为生产力代表，Kimi Code、ZCode、Qoder、Hermes Agent 为 cowork 代表）

## 任务二维刻画

- **任务复杂度**：决策数量与相互依赖、需探索的分支、需组合的工具与证据
  （软件开发/研究/设计即使不等待外部事件也 demanding）
- **时间跨度**：目标保持活跃多久（含等待计算/人/环境变化）
- **RSI（递归自我改进）= 两维交点**，被选为"终极用例"

## 三大设计

### ① 三层运行时：Main Agent + Coordinator Runtime + Subagents

- **Main**：对用户的责任点；解释指令、**自己也会直接执行**（全套工具 + 按需加载
  skill 文档）、修订进行中的工作；完整任务上下文留在 Main
- **Coordinator**：**非 LLM 的控制平面**——组装委派（目标+有界背景+能力授权）、
  持久化任务/执行状态/待反馈（稳定身份、队列、租约、确认），在"安全的模型决策边界"投递消息
- **Subagents**：有界委派、隔离上下文；委派 = **证据契约**（目标、约束、能力、
  期望证据形式）；返回 报告 + 产物引用 + 执行状态（"做了什么 + 什么仍不确定"）
- 同步/异步双模式：异步委派立即返回 ack + id，结果进 Main 的 **Inbox**，
  可触发后续 Main turn；Main 可等待选定/全部未完成委派
- **Subagent↔Subagent 最小 peer 通道**：经 Coordinator 持久化投递，异步非阻塞，
  解决局部依赖但不形成第二控制层
- Subagent 可向 Main 发起**决策请求**：带超时 + 声明的回退假设，不无限持有运行时

### ② 可扩展应用生态：共享应用对象 + 资源协议（最大差异化点）

- 资源 r = (身份, 类型, 内容, **已提交修订号**)，生命周期独立于任何会话/应用窗口
- **乐观并发**：人改与 Agent 改走同一条提交规则——基线修订过期 → 冲突而非静默覆盖；
  每次接受的变更记录 actor 与摘要 → **人机混编编辑变成可审计的变更序列**
- **原生应用**：文本文档、PPTist 演示、参数化 CAD、Blender——人直接改一段话/挪一个
  物体/调一个参数，Agent 通过领域工具观察提交后的状态。
  ***"人的编辑就是经由产物本身表达的任务指导"***，而非单独的指令通道
- Subagent 只能写**私有候选区**；Main 审核后候选才进入共享产物（重新过并发检查）
- **MCP 集成**既有软件（FreeCAD/Blender 外部窗口、CAD/Office 内嵌视图）；
  应用自管文档的所有权边界保持清晰
- 浏览器/桌面操作是**正交交互模式**（语义/视觉观察驱动），人可随时暂停接管窗口

### ③ 持久工作区状态管理

- Task 状态 z = (工作对象 W, 进度/决策/证据 P, 进行中执行 R)；Run = Task 内一次具体执行
- **Jobs**：长计算跑成后台 Job（持久身份、进程记录、日志），跨轮次/上下文重置/重启存活
- **确定性监控源**：脚本采样对比基线，**无变化不烧模型**；候选事件直送 Main 或先语义分析；
  采样节奏与 Main 的定时目标审查相互独立
- **停车协议**：Main 挂起前必须记录进度/等待条件/下一步；必须挂一个活跃观察源或
  显式 review 时间，否则运行时**拒绝该等待**
- **检查点与恢复**：消息轨迹 + 压缩摘要 + 游标一起持久化；supervisor 在有待办工作时
  重新激活缺席的 Main；恢复后先检查外部世界是否已推进
- **Task Memory 文档**：目标/已完成/下一步/重要约束；上下文重建
  （语义记忆 ⊕ 运行时投影 ⊕ 实验摘要 ⊕ 近期历史，token 有界）+ 安全边界压缩
- **实验历史账本**：记录实验目标/委派 Run/候选变体/产物/指标；
  **区分"从 run 产出的指标文件读到的测量值"与"叙述报告里声称的值"**

## 实验

### 12 benchmark / 4 域横向对比

统一模型 **GLM-5.3**（High thinking，256K 上下文，64K 输出），对比 OpenHands、Codex、
DeepSeek Harness（DSH）：

| 域 | OpenHands | Codex | DSH | CoworkOS |
|---|---|---|---|---|
| Tool Use（SkillsBench/τ3-Banking/AutomationBench） | 51.33 | 43.40 | **55.31** | 55.22 |
| Productivity（JobBench/Harvey/GDPVal/OfficeQA/WorkspaceBench） | 55.27 | 40.11 | 56.13 | **57.54** |
| Search（WideSearch/BrowseComp） | **82.22** | 78.78 | 76.64 | 82.14 |
| ML Coding（MLE-Bench-Lite/MLS-Bench） | 44.66 | 48.17 | 56.14 | **56.26** |

→ 每个域前二；Productivity 与 ML Coding 第一，Tool Use 与 DSH 基本打平，
Search 与 OpenHands 打平

### 24 个真实产物案例（定性）

4 前端网站 + 4 游戏 + 4 Office 演示 + 8 交互可视化 + 2 CAD 装配 + 2 Blender 场景，
与 Codex/DSH 产物逐案对比（信息组织、场景细节、可读性、材料/结构表达）

### RSI 原生后训练（旗舰演示）

- 闭环：模型评估 → 能力缺口诊断 + 数据管线修订 → 数据生成/验证/过滤 → SFT → 再评估
- GLM-5.3 当控制器/数据教师，**Qwen3.6-35B-A3B** 为训练目标
- 5 轮：8 个 benchmark 均值 **33.19 → 42.95（+9.76）**；7/8 项超初始基线
- 机制细节：按 benchmark 表现定数据优先级（Automation 占 78.3% 轨迹后持续加大）；
  针对失败模式改示范（逐项处理、约束检查、执行后回读）；监督分布审计
  （42 条长轨迹占 84.2% 监督 token → 扩大独立集到 558 条）；BM25 去污染

## 对比：CoworkOS vs DeepSeek Super Agent

| 维度 | DeepSeek Super Agent（2026-10-31） | CoworkOS（2026-11） |
|---|---|---|
| **出发点** | 瓶颈是**人的注意力**（一人跟 3–5 个 session）→ 系统接管会话管理，人上移到决策/验收 | 任务空间 = **复杂度 × 时间跨度** → 一个 agent 同时扛复杂产物生产 + 持续监控决策 |
| **架构** | **两层**：Router（持久 LLM session，管交互+分派，**自己不执行**，可用轻模型）+ Workers（隔离、全套工具、最强模型、不对人说话） | **三层**：Main（LLM，**自己也执行**）+ Coordinator（**非 LLM 控制平面**）+ Subagents（证据契约委派） |
| **关键结构差异** | 协调者本身就是 LLM session | **LLM（Main）与控制平面（Coordinator）分离**——协调是运行时逻辑，不是模型推理 |
| **Worker 间通信** | Worker 一般不直接通信，冲突出现在共享环境（如同一 PR） | 有**最小 peer 通道**（经 Coordinator 持久投递） |
| **人的接口** | **通道**：IM（≤100 词轻打扰）+ GitHub/邮件（结构化高密度）；全双工 start()/stop() | **对话 + 共同编辑同一产物**：资源协议 + 修订控制，人改 CAD 参数 = 任务指导 |
| **主动性** | Trigger 事件驱动 + 策略过滤 + **Acting in Advance**（投机执行，静默准备，有价值才交付） | 后台 Jobs + **确定性监控源**（无变化不烧模型）+ 定时 goal review + 停车协议 |
| **监督/恢复** | **Watchdog**：独立外部 Agent session，模型判断 Worker 实质推进/目标漂移，可自然语言运维 | supervisor 重新激活缺席 Main + 检查点恢复（轨迹+摘要+游标）+ 完成度检查 |
| **记忆** | 存算分离、Chain of Recall（架构原则层面） | 更**机制化**：Task Memory 文档、上下文重建公式、压缩游标、**实验账本**（测量值 vs 叙述值） |
| **部署** | 云 Agent Loop + 设备端 daemon（计算/状态/环境三分离），跨设备持久 | 聚焦 harness/workspace，不强调跨设备/云边部署 |
| **生态** | 开发协作为主（IM+GitHub），个人助理是延伸；MIT 开源原型 | **生产力生态更广**：原生文档/PPT/CAD/Blender + MCP + 浏览器/桌面；RSI 后训练旗舰 |
| **实验性质** | 消融（p95 29.5→15.7min、batching）——**测调度，不测产物质量** | 12 benchmark 横向对比（受控同模型）+ 产物案例 + RSI +9.76 |
| **一句话** | "注意力经济"的**运营模型**：agent 如何 7×24 自主运转、少打扰人 | "共享工作区"的**协作模型**：人和 agent 如何在同一批演化产物上持续共事 |

## 判断

1. **互补而非竞争**：DSH 回答"agent 怎么持续自主地活"（事件驱动、通道适配、
   Watchdog）；CoworkOS 回答"人和 agent 怎么在同一产物上共事"（共享修订、证据契约、
   工作区记忆）。CoworkOS 论文里 DSH 既是引言代表系统，也是 benchmark 基线
2. **对比是受控的**：四个 harness 都跑 GLM-5.3；DSH 内部部署用 DeepSeek 系模型，
   且其自家实验只验证调度效率——"谁更强"不能直接从表里读出
3. **对 TV personal agent 的启示**：DSH 提供**运营骨架**（电视为新通道、屏幕状态/
   语音/日程为 Trigger、静默主动性、Watchdog）；CoworkOS 补上**家庭共享工作区**
   （家庭日历/购物清单/观影计划作为共享应用对象，家人直接改 = 下指令，修订控制防
   多人多设备覆盖；确定性监控源不烧模型；长任务跑成 Jobs）——见
   [06-tv-personal-agent.md](06-tv-personal-agent.md)
4. **注意**：网上有同名 **CoWork OS 桌面应用**（<https://coworkosapp.com>，
   macOS/Windows 开源 AI 桌面，"Rent the model. Own everything else."），
   论文未提及它，两者关系未证实，勿混淆

## 参考

- 论文：<https://arxiv.org/abs/2611.03178>
- HuggingFace：<https://huggingface.co/papers/2611.03178>
- alphaXiv：<https://www.alphaxiv.org/abs/2611.03178>
