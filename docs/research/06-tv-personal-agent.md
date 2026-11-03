# TV Personal Agent 设计方案

## 竞争格局：大厂在做什么

| 玩家 | 形态 |
|---|---|
| Samsung Vision AI | "世界首个多 AI Agent TV 平台"：集成 Microsoft Copilot + Perplexity，Google Gemini 驱动；情境感知、不中断画面 |
| 海信 | 电视行业 12 大 AI 智能体（影视/游戏/体育/健康…垂类） |
| Coolita（大屏 AI OS） | 微软 ASR（77 语言）+ AI 影视搜索/绘本创作/生活助手/艺术壁纸生成 |
| Google Gemini for TV | Android 14+ / 2GB RAM 的 Google TV 设备，18+ 国家 |
| Displace（CES 2026） | 首款 AI-native TV：**pause-to-shop**（暂停时按个人偏好推场景商品）、个性化视频新闻 Agent |
| Fetch.ai + RedSquid TV | agentic AI TV 平台（2026-07） |
| Glance + Google Cloud | 锁屏 + **ambient TV 屏**的生成式 AI 体验 |

**共性：屏内助手 + 垂类 Agent（功能）**。
**缺口：跨内容、跨家庭成员、跨设备的持久个人实体（personal agent）**——
Muse 在个人手机/电脑侧做了，TV/家庭侧还没有人做。

## 市场信号（双刃）

- 有人用 AI agent 把智能电视"变笨"（[dumbtv](https://techcrunch.com/2026/10/19/people-are-using-ai-to-make-their-smart-tvs-dumb-again)）——隐私反弹真实存在
- Anthropic 警告小模型可能在智能电视/家电上**自主复制**（AI worm 风险）
- 结论：**隐私与可控性是生命线，不是 feature**

## 定位

TV = 家庭的**大屏个人 Agent**：
- Muse 的"个人 Agent"（为目标主动做事）
- nanoMuse 的"一个 Agent 跨所有设备"（四句话定义：有名字形象、会做事、关 app 继续干、
  记住你、不可逆操作先问）
- DeepSeek Super-agent 的"7×24 事件驱动自主运转"

一句话差异化：大厂做的是屏内"功能"，tvagent 做的是**有名字、有记忆、有主动性、
有边界的持久实体**。

## 架构（复用 dsh-superagent 运营骨架 + CoworkOS 协作机制）

### 1. 双层架构不变

- **Router** = 家庭统一入口（持久全局上下文：谁在看什么、家庭日程、进行中任务）
- **Workers** 隔离并行：内容检索 / 购物 / 日程 / 家居控制 / 新闻摘要
- 电视只是 Router 的**通道之一**（对应 DSH 的 IM+GitHub 双通道）

### 2. Trigger 源（与屏内助手的核心区别）

| Trigger | 说明 |
|---|---|
| 语音 | 遥控器/常听；端侧 ASR（Sensory / Picovoice） |
| **屏幕状态** | 正在播什么、暂停、换台、停留时长 → content-aware |
| 家庭日历 | 学校日程、球赛、家庭聚会 |
| 智能家居状态 | 人回家、熄灯=要睡了 |
| 外部事件 | 新闻、天气、快递、订阅续费 |

### 3. 有边界的主动性

- **Acting in Advance**（DSH）：后台静默准备（推荐、日程冲突、购物清单草稿），
  有价值才交付，否则沉默
- **"asks before anything you cannot undo"**（nanoMuse）：购买、删除、儿童可见性
  变更必须显式批准（Sentinel 式 scoped approval）
- **按家庭成员分权限**：儿童 profile、消费上限、内容分级

### 4. 家庭记忆（CoworkOS 共享工作区）

- 成员画像（口味、忌口、作息）、观看历史、家庭事务
- **共享应用对象**（CoworkOS 资源协议）：家庭日历、购物清单、观影计划、家居方案
  作为持续演化的产物——**家人直接改 = 给 agent 下指令**，修订控制防多人多设备
  互相覆盖，变更可审计
- TV 是天然的"家庭记忆"终端：共享屏幕、多用户
- 记忆实现可参考 nanoMuse：Markdown 文件（人类可读可编辑）或结构化存储

### 5. 手（动作层）

- 屏幕：UI 自动化（换台/音量/启 app/投屏）
- 语音：端侧 ASR + TTS，全双工对话（DSH 异步 start()/stop()）
- 智能家居：Matter / HomeKit / 厂商 API
- 商务：pause-to-shop（Displace 已验证）→ 电商、订阅管理
- **跨设备：手机当管理通道**——电视负责显示，手机负责审批和深交互

### 6. 监控与长任务（CoworkOS）

- **确定性监控源**：快递到了没有、球赛开始了没有——脚本采样对比基线，
  无变化不烧模型
- **Jobs**：等剧更新、盯安装师傅、盯训练——长任务持久身份 + 日志，跨重启存活
- **Watchdog**（DSH）：外部控制回路，判断 agent 是否实质推进/目标漂移

### 7. 隐私即架构

- Muse Secure VM 思路：agent + 数据隔离在专属 VM / 本地盒子
- 端侧小模型管唤醒与隐私敏感环节；云端大模型管推理 + 数据最小化策略
- 显式数据边界："屏幕前看到什么不出门"（或匿名化）
- 凭据"用而不碰"（DSH 开放问题）

### 8. 模型策略

- model-agnostic（Coworker 经验：新模型自动接入，无厂商锁定）
- Router 轻模型 + Worker 强模型（DSH 结论）

## 落地路径

| 阶段 | 内容 | 周期 |
|---|---|---|
| **Phase 0** | 单户 MVP：现成 Android TV/盒子 + 语音 + 屏幕状态 + 内容推荐 + 一个购物场景。改造现有 dsh-superagent 部署：电视作为新 Source（通道），屏幕状态/语音作为 Trigger | 2–4 周 |
| **Phase 1** | 家庭记忆 + 成员画像 + 主动性策略（静默准备、审批闸门、儿童权限） | — |
| **Phase 2** | 智能家居 + 手机管理通道 + 商务闭环（pause-to-shop → 支付） | — |

**评测**：VibeLifeBench（主动+持久，living world）/ π-Bench（长程工作流）/
Claw-Anything（always-on + 无关事件干扰）

**风险**：
- TV OS 碎片化（Tizen / webOS / Android TV）
- 端侧算力限制（唤醒/ASR 必须端侧）
- 隐私监管（家庭场景敏感数据：儿童、观看行为）
- 大厂平台锁定（Samsung/Google 生态封闭性）

## 参考

- 调研全文：[01](01-deepseek-super-agent.md) / [02](02-coworker-landscape.md) /
  [03](03-meta-muse.md) / [04](04-nanomuse.md) / [05](05-coworkos-vs-superagent.md)
- dumbtv：<https://techcrunch.com/2026/10/19/people-are-using-ai-to-make-their-smart-tvs-dumb-again>
- Samsung Vision AI：<https://www.samsung.com/us/tvs/vision-ai-tv>
- Displace（CES 2026）：<https://finance.yahoo.com/news/displace-reimagines-future-tv-ces-140000033.html>
- 端侧语音 Agent：<https://sensory.com/solution/llm-voice-agents> · <https://picovoice.ai/cookbook/llm-voice-ai-agent-assistant>
