---
name: agent-cleaner-pro
description: "Use when managing Hermes Agent conversation sessions — cleanup old sessions, classify by type, distill valuable content into knowledge base, and schedule automatic maintenance. Like CCleaner but for AI conversations. 智能体清理大师。"
version: 1.0.0
author: tobiglevent001
license: MIT
metadata:
  hermes:
    tags: [session-management, cleanup, knowledge-base, conversation-distillation, cron, maintenance]
    related_skills: [skill-dev-or-not, hermes-agent]
---

# Agent Cleaner Pro — 智能体清理大师

## Overview

Hermes Agent 对话会随着使用不断积累，本 Skill 帮助用户从"不敢清理"变成"智能管理"。核心是三步闭环：

> **提炼精华 → 归档知识库 → 清理冗余**

不只是删对话，而是把有价值的决策、代码、配置提炼出来永久保存，把无用的垃圾清掉。

Based on the reference implementation: https://github.com/tobiglevent001/agent-cleaner-pro

## When to Use

- 用户说"帮我清理一下对话" / "clean up my sessions"
- 用户感觉对话太多、session_search 太慢
- 用户想整理项目知识，把散落在对话中的信息归档
- 对话超过 30 个，或占用空间超过 20MB
- 用户想设置定期自动清理维护

**Don't use for:** 单个会话的手动查看（用 `hermes sessions browse` 即可）

## How It Works

### 第一阶段：扫描与分类

执行 `hermes sessions list` 获取所有会话，然后对每个会话分类：

| 类型 | 代码 | 特征 | 建议保留 |
|------|------|------|---------|
| 📁 长期项目 | PROJECT | 标题含项目名、轮数>20、含代码/架构讨论 | 永久 / 1年+ |
| 🗑️ 一次性问答 | QUICK_QA | 标题为简单问题、轮数<5、无代码 | 7天 |
| 🧩 超长混合 | MIXED_LONG | 轮数>20、混合多种话题 | 提炼后删除 |
| 📋 配置调试 | CONFIG_DEBUG | 标题含配置/调试/切换关键词 | 30天 |
| ❓ 未分类 | UNKNOWN | 标题为"—"或无明确特征 | 扫描后人工判断 |

### 第二阶段：生成可视化清理清单

类似 CCleaner / 电脑管家风格，按类型分组显示：

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  🧹 智能体清理大师 — 对话清理报告
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📁 长期项目 (建议保留)         3个   15.2 MB
  [🔒] Smart Model Selector Skill   5.2 MB  🔒
  [✓] GEO监测系统开发               8.5 MB  永久保留
  ...

🗑️ 一次性问答 (建议删除)       8个    1.8 MB
  [☐] 明天上海什么天气             0.1 MB  ⚡可删
  ...

操作: [1] 清理勾选  [2] 全部推荐  [3] 提炼+归档+清理
```

### 第三阶段：智能提炼

对"超长混合"类型会话，执行提炼：

```bash
hermes sessions export --session-id X - | ...
# → 读取完整会话 → LLM 提取关键信息
# → 写入 ~/hermes-knowledge/{project}/
```

**提炼包结构：**
```
~/hermes-knowledge/{项目名称}/
├── summary.md         ← 会话摘要
├── decisions.md       ← 关键决策
├── code-snippets/     ← 代码片段
├── architecture.md    ← 架构决策
└── meta.json          ← 源会话元数据
```

### 第四阶段：安全清理

```bash
# 执行删除
hermes sessions delete --session-id X

# 或者批量清理旧会话
hermes sessions prune --older-than 30 --yes
```

**安全机制：**
- 白名单保护（🔒 标记的会话永不删除）
- 删除前二次确认
- 可选先用 `hermes backup` 备份

### 第五阶段：定时维护

设置 cron 定期自动扫描：

```bash
hermes cron create \
  --schedule "0 10 * * 0" \
  --name "agent-cleaner-check" \
  --skill agent-cleaner-pro \
  --prompt "扫描所有会话，生成清理报告，列出需要关注的内容但不执行删除"
```

## Token Cost Reference

| 操作 | 预估消耗 | 成本 (DeepSeek) |
|------|---------|----------------|
| 扫描+分类 (50个会话) | ~6K tokens | ~¥0.001 |
| 生成清理清单 | 0 (CLI) | ¥0 |
| 提炼1个长会话 | ~53K tokens | ~¥0.008 |
| 全量清理 (首次) | ~270K tokens | ~¥0.05 |
| 每周维护 | ~10K tokens | ~¥0.002 |

## Commands Reference

```bash
# 查看所有会话
hermes sessions list

# 查看统计信息
hermes sessions stats

# 导出某个会话
hermes sessions export --session-id <ID> -

# 删除某个会话
hermes sessions delete --session-id <ID>

# 批量删除旧会话
hermes sessions prune --older-than 30 --yes

# 清理前备份
hermes backup -o ~/hermes-backup-$(date +%Y%m%d).zip

# 设置定时任务
hermes cron create ...
```

## Common Pitfalls

1. **一开始就自动删除** — 任何 cleanup 操作都要先展示清单让用户确认，不搞 silent delete
2. **忽略白名单** — 重要的项目对话必须用 🔒 保护，防止误删
3. **提炼不充分** — 长对话提炼时，确保输出包含：决策理由、代码片段、配置文件路径，缺一不可
4. **只看大小不看价值** — 有些小对话（如配置参数）价值很高，不要因为小就删
5. **忘了备份** — 大清理前先用 `hermes backup` 备份，给用户留后路
6. **把用户的记忆（memory）和会话（session）搞混** — memory 是持久化的用户信息，session 是对话记录。本 Skill 只管理 session。

## Related

- **GitHub Repository (reference implementation):** https://github.com/tobiglevent001/agent-cleaner-pro
- **skill-dev-or-not:** This skill was evaluated by skill-dev-or-not before development

## Verification Checklist

- [ ] 所有会话已扫描并分类
- [ ] 清理清单已按类型分组展示
- [ ] 每个会话有大小、类型、建议操作
- [ ] 用户确认后才执行删除
- [ ] 重要会话已加入白名单（🔒）
- [ ] 提炼内容已归档到 ~/hermes-knowledge/
- [ ] 删除操作已通过二次确认
- [ ] cron 定时任务（如设置）已验证可用
