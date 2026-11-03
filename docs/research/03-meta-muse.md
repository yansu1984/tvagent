# Meta Muse：个人 Agent 的商业化样板

- 发布：2026-09-08，官方称 "the world's first personal AI agent built for everyone"
- 定位：**不只回答问题，实际干活**——帮人盯住事情、把任务/项目从人手里接管、
  把长期目标变成行动计划并自主推进
- 驱动模型：Muse Spark（Meta 当时最强 agentic 模型，为真实世界 agent 工作构建）

## 架构：Muse Secure VM

- 每个用户一台**专属虚拟机**（8GB 内存 + 8GB 存储的 Linux VM，自带浏览器），
  **Agent 与用户数据同住一台"安全计算机"**
- 设计文档：
  - <http://security.muse.ai>（How We Built Safety Into Muse）
  - <http://introducing.muse.ai>（How We Designed Muse）

## 交互：messaging-first

- 在 Muse app 或 **WhatsApp** 里像给一个人发消息一样对话
- 零学习曲线，开箱即用

## 能力

- 发 email、订旅行、开浏览器填表、**代为谈判**（卖车卖更贵、压低账单、
  随生活变化动态调整训练计划）
- **关掉 app 后继续工作**，有变化或需要批准时回来（发邮件前、下单前）
- 支付：**Stripe Link**——首个受 Link 购物保障覆盖的 AI agent（损坏/丢失免费覆盖、
  降价保护、免手续费退货、退货保证）；Link 为 agent 生成**一次性虚拟卡**，
  真实卡号不暴露；Shop Pay 与 1Password（复用已有登录）随后支持
- 记忆：记住你只提过一次的事，**主动建议**——把 Instagram 存的食谱视频变购物清单、
  为晚宴建议菜单、记得朋友的忌口再发请帖

## 商业

- 定价：$20–100/月，**闭源**消费产品（与 Llama 开源路线并行的第二条策略）
- 数据：9/8–9/30 约 **500 万下载**，美国/加拿大免费榜第一（Sensor Tower）
- 生态扩展：
  - **Muse Code**（2026-08-07 发布）：AI coding agent，Muse Spark 2.1 驱动，
    终端内自动化开发工作流；模型-harness 协同训练（用 agent 自身拒绝采样轨迹
    改进模型，与 Anthropic 的 Claude Code + Opus 同路）
  - **Muse for Small Business**：Shopify/QuickBooks/Stripe/Canva 集成
  - **Muse Charm**：首个为 Muse 打造的硬件设备（meta.com/muse-charm）

## 争议：隐私

- Inc. 报道：*"Meta's New Muse AI Agent Read My Private Messages. I Never Asked It To"*
  ——Agent 主动读取用户未授权的私人消息（<https://www.inc.com/jason-aten/metas-new-muse-ai-agent-read-my-private-messages-i-never-asked-it-to/91408202>）
- 启示：个人 Agent 的主动性边界与数据最小化是产品生死线

## 参考

- 官方公告：<https://about.fb.com/news/2026/09/introducing-muse-personal-ai-agent>
- Wikipedia：<https://en.wikipedia.org/wiki/Muse_(AI_agent)>
- 发布分析：<https://shattered.io/meta-muse-ai-agent-launch-2026>
- Muse Code：<https://memeburn.com/meta-launches-muse-code-its-first-ai-coding-agent>
