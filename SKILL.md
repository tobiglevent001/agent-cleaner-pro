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

Hermes Agent 对话会随着使用不断积累。本 Skill 帮助用户从"不敢清理"变成"智能管理"。
核心三步闭环：

> **提炼精华 → 归档知识库 → 清理冗余**

不只是删对话，而是把有价值的决策、代码、配置提炼出来永久保存，把无用的垃圾清掉。

Based on the reference implementation: https://github.com/tobiglevent001/agent-cleaner-pro

## When to Use

- 用户说"帮我清理一下对话" / "clean up my sessions"
- 用户感觉对话太多、session_search 太慢
- 用户想整理项目知识，把散落在对话中的信息归档
- 对话超过 30 个，或数据库超过 20MB
- 用户想设置定期自动清理维护

**Don't use for:** 单条消息查看（用 `hermes sessions browse`）或紧急调试（先修 bug）

## Workflow

本 Skill 严格按 5 阶段流程执行，每个阶段需等待用户确认后再进入下一阶段。

### 阶段 1：扫描与分类

**输入：** 无（自动扫描全部会话）
**输出：** 分类结果（5 种类型）

**执行步骤：**
1. `hermes sessions stats` → 获取总数、消息数、数据库大小
2. `hermes sessions list --limit 200` → 获取所有会话标题和 ID
3. 对每个会话按分类规则分类（详见 `references/classification-guide.md`）

**分类决策树（简版）：**
```
会话标题是否为"—"?
├─ 是 → 查看预览文本
│   ├─ 含"能用吗/什么模型/测试"等 → QUICK_QA
│   └─ 含"hermes doctor/配置"等 → CONFIG_DEBUG
└─ 否 → 查看标题
    ├─ 含项目名+轮数>20 → PROJECT
    ├─ 含"配置/调试/切换/升级" → CONFIG_DEBUG
    ├─ 简单问题(<5字) → QUICK_QA
    ├─ 混合话题+轮数>20 → MIXED_LONG
    └─ 其他 → UNKNOWN
```

**输出示例：**
```
📊 扫描完成：49个会话，2584条消息，29.1MB
```

### 阶段 2：生成可视化清理清单

**输入：** 分类结果
**输出：** 分类+大小+建议操作的清理报告

**报告格式：**
```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  🧹 智能体清理大师 — 对话清理报告
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📁 长期项目 (建议永久保留)          N个   XX MB
  [🔒] 会话标题1                     XX MB  🔒永久保留
  [🔒] 会话标题2                     XX MB  🔒永久保留

📋 配置调试 (30天后可清理)          N个   XX MB
  [☐] 会话标题1                     XX MB  ⚡保留至 YYYY-MM-DD

🗑️ 一次性问答 (建议立即清理)       N个   XX MB
  [☐] 会话标题1                     XX MB  ⚡N天前

🧩 超长混合 (需提炼后清理)         N个   XX MB
  [🔍] 会话标题1                     XX MB  → 待提炼

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 汇总:
  永久保留:  N个会话 (重要项目)  🔒
  待提炼:    N个会话 (需先提炼再删)
  可清理:    N个会话 (XX MB可释放)  ⚡

操作: [1] 清理勾选的内容  [2] 提炼+归档+清理
      [3] 查看详情  [4] 先备份再清理  [5] 取消
```

**关键规则：**
- 🔒 标记的会话永不进入删除候选（除非用户手动移除白名单）
- 详细分类规则见 `references/classification-guide.md`
- 报告模板见 `templates/cleanup-report.md`

### 阶段 3：智能提炼

**输入：** 用户选择的 MIXED_LONG 类型会话
**输出：** 提炼文档包 → 写入 `~/hermes-knowledge/`

**提炼步骤：**
1. `hermes sessions export --session-id <ID> -` → 导出完整会话
2. 使用提炼 prompt（见 `references/distillation-prompt.md`）提取关键信息
3. 创建知识库目录结构
4. 写入提炼结果

**知识库归档结构：**
```
~/hermes-knowledge/
├── {项目名称-中文}/
│   ├── meta.json           ← 源会话元数据（ID、时间、模型）
│   ├── summary.md          ← 会话摘要（做了什么、解决了什么）
│   ├── decisions.md        ← 关键决策（为什么选A不选B）
│   ├── code-snippets/      ← 有价值的代码片段
│   └── architecture.md     ← 架构决策（系统设计、数据流）
├── index.json              ← 总索引（所有提炼包的可搜索索引）
```

**提炼输出要求（必须包含）：**
- ✅ 项目/功能名称
- ✅ 关键决策及理由（至少 3 条）
- ✅ 代码片段（如有）
- ✅ 架构/设计决策（如有）
- ✅ 配置/参数（如有）
- ❌ 不要包含闲聊内容
- ❌ 不要包含原始对话逐字稿

**详细提炼 prompt 模板见 `references/distillation-prompt.md`**
**知识库模板见 `templates/knowledge-base/`**

### 阶段 4：安全清理

**输入：** 用户确认的清理清单
**输出：** 已删除的会话 + 释放的磁盘空间

**安全机制（四级防护）：**
1. **白名单保护** — 🔒 标记的会话永不进删除列表
2. **二次确认** — 展示"将删除 N 个会话 (X MB)"要求确认
3. **备份兜底** — 自动建议 `hermes backup`（可选）
4. **增量删除** — 一次最多删 10 个，分批确认

**执行步骤：**
```bash
# 可选：先备份
hermes backup -o ~/hermes-backup-$(date +%Y%m%d_%H%M%S).zip

# 确认删除
echo "⚠️ 将删除 N 个会话 (X MB)，不可恢复！确认？(y/N)"
# 用户输入 y 后执行
hermes sessions delete --session-id <ID1>
hermes sessions delete --session-id <ID2>
# ...

# 验证
hermes sessions stats
```

### 阶段 5：定时维护（cron）

**输入：** 用户同意
**输出：** cron 定时任务

**配置流程：**
1. 询问用户希望的频率
2. `hermes cron create` 创建任务
3. 验证任务激活

```bash
hermes cron create \
  --schedule "0 10 * * 0" \
  --name "agent-cleaner-weekly" \
  --skills agent-cleaner-pro \
  --prompt "执行智能体清理大师的阶段1-2：扫描所有会话并生成清理报告。不要执行删除，只输出报告供用户查看。"
```

**详细配置指南见 `references/cron-setup.md`**

## Token Cost Reference

| 操作 | Input Tokens | Output Tokens | 成本 (DeepSeek) |
|------|-------------|--------------|----------------|
| 阶段1：扫描+分类(50会话) | ~5K | ~1K | **¥0.001** |
| 阶段2：生成报告 | 0 (CLI) | 0 | **¥0** |
| 阶段3：提炼1个长会话 | ~50K | ~3K | **¥0.008** |
| 阶段3：提炼5个长会话 | ~250K | ~15K | **¥0.04** |
| 阶段4：执行删除 | 0 (CLI) | 0 | **¥0** |
| 阶段4：备份(可选) | 0 (CLI) | 0 | **¥0** |
| 阶段5：cron创建 | <1K | <1K | **¥0.0002** |
| **首次全量清理合计** | **~260K** | **~17K** | **~¥0.05** |
| **每周维护** | **~10K** | **~2K** | **~¥0.002** |

## Commands Quick Reference

```bash
# 查看会话
hermes sessions list --limit 200    # 列出所有会话
hermes sessions stats               # 查看统计信息
hermes sessions export --session-id <ID> -   # 导出某个会话

# 删除会话
hermes sessions delete --session-id <ID>     # 删除单个会话
hermes sessions prune --older-than 30 --yes  # 批量删除旧会话

# 备份
hermes backup -o ~/hermes-backup-<DATE>.zip

# 定时任务
hermes cron list                    # 查看已有任务
hermes cron create ...              # 创建任务
hermes cron remove <JOB_ID>         # 删除任务
```

## Common Pitfalls

1. **silent delete** — 任何清理都必须先展示清单让用户确认，绝不自动删除
2. **白名单遗漏** — 用户说过的项目名/关键词即使没在标题中体现，也要主动询问是否加入白名单
3. **提炼不完整** — 长对话提炼必须包含：决策理由、代码片段、配置路径、架构图（如有），四者缺一不可
4. **只按大小判断** — 有些小对话含关键配置参数，价值远大于一堆测试对话。分类优先看内容而非大小
5. **忘记备份** — 单次删除超过 5 个会话时，先用 `hermes backup` 备份
6. **memory vs session混淆** — memory 是用户画像/偏好/事实，session 是对话记录。本 Skill 只管理 session，不碰 memory

## References

- `references/classification-guide.md` — 详细的会话分类决策树和规则
- `references/distillation-prompt.md` — 智能提炼的 LLM prompt 模板
- `references/cron-setup.md` — 定时维护配置指南
- `templates/cleanup-report.md` — 清理报告模板
- `templates/knowledge-base/` — 知识库归档模板

## Verification Checklist

- [ ] 阶段1：所有会话已扫描并分类，无遗漏
- [ ] 阶段2：清理清单已按类型分组展示，每个会话有大小和操作建议
- [ ] 阶段2：白名单（🔒）正确标记受保护会话
- [ ] 阶段3：提炼内容包含决策/代码/架构/配置四要素
- [ ] 阶段3：知识库归档到 ~/hermes-knowledge/，结构完整
- [ ] 阶段4：用户确认后才执行删除，二次确认生效
- [ ] 阶段4：批量删除>5个时建议了备份
- [ ] 阶段5：cron 任务已创建并验证激活
- [ ] 每个阶段完成后显示了统计对比（before/after）
