# Agent 产品与论文调研（2026-11）

本目录是 tvagent（TV personal agent）立项前的调研档案，覆盖四款代表性系统、
人大/复旦最新 coworker 论文、CoworkOS 论文对比，以及 TV personal agent 的设计方案。

| 文档 | 内容 |
|---|---|
| [01-deepseek-super-agent.md](01-deepseek-super-agent.md) | DeepSeek Super-agent：设计文解读（双层架构、事件驱动、Watchdog 等五大模块） |
| [02-coworker-landscape.md](02-coworker-landscape.md) | Coworker（AI 数字同事）：产品格局 + 学术进展 + 人大/复旦最新论文 + 个人 Agent 基准 |
| [03-meta-muse.md](03-meta-muse.md) | Meta Muse：个人 Agent 的商业化样板（Secure VM、messaging-first、支付闭环） |
| [04-nanomuse.md](04-nanomuse.md) | nanoMuse：Muse 的开源对标（跨设备、Sentinel 审批、Markdown 记忆） |
| [05-coworkos-vs-superagent.md](05-coworkos-vs-superagent.md) | CoworkOS 论文（arXiv:2611.03178，人大+复旦）调研 + 与 DeepSeek Super Agent 逐项对比 |
| [06-tv-personal-agent.md](06-tv-personal-agent.md) | TV personal agent 设计方案：定位、架构、Trigger、记忆、隐私、落地路径 |

## 核心结论（TL;DR）

1. **行业拐点**：2026 年被视为"数字员工/个人 Agent 元年"。瓶颈从模型能力转向
   **人的注意力**（DeepSeek）与**人机共享工作区**（CoworkOS）两条主线并行。
2. **两套互补的架构范式**：
   - DeepSeek Super-agent = "注意力经济"的**运营模型**：7×24 事件驱动、双层
     Router/Worker、静默主动性、外部 Watchdog。
   - CoworkOS = "共享工作区"的**协作模型**：Main/Coordinator/Subagents 三层、
     人与 Agent 共同编辑同一产物（修订控制 + 审计）、持久工作区与实验账本。
3. **TV 的机会**：大厂做的是"屏内助手 + 垂类 Agent"（Samsung/海信/Coolita/Google），
   缺口是**跨内容、跨家庭成员、跨设备的持久个人实体**。TV = 家庭共享工作区的
   天然终端（共享屏幕、多用户、语音 + 屏幕状态双输入）。
4. **隐私是生命线**：dumbtv 隐私反弹 + Anthropic 智能电视 AI worm 警告 →
   端侧小模型 + 数据最小化 + "不可逆操作先询问"必须写进架构，而不是当 feature。
5. **落地捷径**：本部署已在运行 dsh-superagent（DeepSeek Harness），TV personal
   agent 的运营骨架可直接复用——电视作为新通道（Source），屏幕状态/语音/日程作为
   Trigger，再叠加 CoworkOS 式的家庭共享工作区即可。
