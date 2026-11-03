# nanoMuse：Muse 的开源对标

- 仓库：<https://github.com/nano-muse/nanoMuse>（GPL-3.0，2.9k stars / 342 forks /
  2038 commits）
- 论文：[nanoMuse: An Open-Source Personal Agent for Every Device You Own]
  （arXiv 2610.08699，12 页 6 图 2 表，CC-BY-4.0）
- 项目页：<https://nanomuse.cn/> · [Show HN](https://news.ycombinator.com/item?id=49987765)
- 定位：**Meta Muse 的开源（GPL-3.0）对应物**——"an open-source personal agent
  for every device you own"

## 产品定义（四句话，可直接当 TV agent 的 north star）

> One agent with a name and a look of its own that **does things**,
> **keeps working while the app is closed**, **remembers you**, and
> **asks before anything you cannot undo**.

## 跨设备

- Android、iPhone/iPad、Windows/macOS/Linux、浏览器
- **手机屏幕和电脑就是它的手**（GUI agent / computer-use）
- 可**自托管 relay**，所有设备共享**一条会话**（one shared conversation）

## 机制

- **Sentinel**：每个动作之上的守卫，scoped approval（按范围授权）；
  不可逆操作先询问
- **记忆 = Markdown 文件**（人类可读、可编辑、可版本化）
- **任意模型**（model-agnostic）
- MCP 接入
- 仓库结构：android / web / cloud / harness / nanomuse / docs / tests / site /
  config / scripts（harness 目录与 DeepSeek Harness 同构）

## 与 Muse 的对照

| 维度 | Meta Muse | nanoMuse |
|---|---|---|
| 许可 | 闭源，$20–100/月 | GPL-3.0 开源，自托管 |
| 运行环境 | 云端 Muse Secure VM | 自托管 relay + 本地设备 |
| 设备 | iOS/Android/Web | 全平台（含桌面 Linux/Windows/macOS） |
| 记忆 | 专有 | Markdown 文件 |
| 审批 | 关键操作确认 | Sentinel scoped approval |
| 模型 | Muse Spark（专有） | 任意模型 |

## 参考

- arXiv：<https://arxiv.org/abs/2610.08699>
- HuggingFace：<https://huggingface.co/papers/2610.08699>
