---
name: agent-cleaner-pro
description: "Use when managing Hermes Agent conversation sessions — cleanup old sessions, classify by type, distill valuable content into knowledge base, and schedule automatic maintenance. Like CCleaner but for AI conversations. 智能体清理大师。"
version: 1.1.0
author: tobiglevent001
license: MIT
metadata:
  hermes:
    tags: [session-management, cleanup, knowledge-base, conversation-distillation, cron, maintenance]
    related_skills: [skill-dev-or-not, hermes-agent]
---

# Agent Cleaner Pro — 智能体清理大师

## Overview / 概述

**Chinese:**
Hermes Agent 对话会随着使用不断积累。本 Skill 帮助用户从"不敢清理"变成"智能管理"。
核心三步闭环：**提炼精华 → 归档知识库 → 清理冗余**

不只是删对话，而是把有价值的决策、代码、配置提炼出来永久保存，把无用的垃圾清掉。

**English:**
Hermes Agent conversations accumulate over time. This skill transforms "afraid to clean" into "smart lifecycle management."
Core three-step cycle: **Distill → Archive → Clean**

Not just deleting conversations — extract valuable decisions, code, and configurations into a permanent knowledge base, then safely remove the clutter.

Based on: https://github.com/tobiglevent001/agent-cleaner-pro

---

## When to Use / 使用场景

**Chinese:**
- 用户说"帮我清理一下对话"
- 感觉对话太多、session_search 太慢
- 想把散落在对话中的信息归档到知识库
- 对话超过 30 个，或数据库超过 20MB
- 想设置定期自动清理维护

**English:**
- User says "clean up my sessions" or "organize my conversations"
- session_search feels slow due to too many sessions
- Want to archive knowledge scattered across long conversations
- More than 30 sessions, or database exceeds 20MB
- Want to set up automatic scheduled maintenance

**Don't use for / 不要用于：**
- 单条消息查看（用 `hermes sessions browse`）/ Checking individual messages
- 紧急调试 / Urgent debugging

---

## Workflow / 工作流程

本 Skill 严格按 5 阶段流程执行，每个阶段需等待用户确认后再进入下一阶段。
This skill follows 5 phases strictly. Each phase waits for user confirmation before proceeding.

---

### Phase 1: Scan & Classify / 扫描与分类

**Input:** None (auto-scan all sessions)
**Output:** Classified results (5 types)

**Steps / 执行步骤：**
1. `hermes sessions stats` — Get total count, messages, DB size
2. `hermes sessions list --limit 200` — Get all session titles and IDs
3. Classify each session (see `references/classification-guide.md` for full decision tree)

**Classification Decision Tree / 分类决策树（简版）：**
```
Title is "—" (untitled)?
├─ YES → Check preview text
│   ├─ Contains "can you/hello/test/first time" → QUICK_QA
│   └─ Contains "hermes doctor/config/debug" → CONFIG_DEBUG
└─ NO → Check title
    ├─ Contains project name + >20 rounds → PROJECT
    ├─ Contains "config/debug/upgrade/switch" → CONFIG_DEBUG
    ├─ Simple question (<10 chars) → QUICK_QA
    ├─ Mixed topics + >20 rounds → MIXED_LONG
    └─ Other → UNKNOWN
```

**Output example / 输出示例：**
```
📊 Scan complete: 49 sessions, 2584 messages, 29.1MB
```

---

### Phase 2: Cleanup Report / 可视化清理清单

**Input:** Classification results
**Output:** Grouped report with size +建议+ recommendations

**Report format / 报告格式：**
```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  🧹 Agent Cleaner Pro — Cleanup Report
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📁 Projects (Keep Forever)              N    XX MB
  [🔒] Session Title 1                   XX MB  🔒Keep Forever
  [🔒] Session Title 2                   XX MB  🔒Keep Forever

📋 Config/Debug (Clean after 30d)       N    XX MB
  [☐] Session Title                      XX MB  ⚡Keep until YYYY-MM-DD

🗑️ Quick Q&A (Clean now)                N    XX MB
  [☐] Session Title                      XX MB  ⚡N days ago

🧩 Mixed Long (Distill then Clean)      N    XX MB
  [🔍] Session Title                     XX MB  → Ready to distill

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Summary:
  Keep Forever:  N sessions  🔒
  To Distill:    N sessions  🔍
  Can Clean:     N sessions (XX MB)  ⚡

Options: [1] Clean selected  [2] Distill+Archive+Clean
         [3] View details    [4] Backup then clean  [5] Cancel
```

**Key rules / 关键规则：**
- 🔒 sessions never enter delete candidates (whitelist protected)
- Full rules in `references/classification-guide.md`
- Report template in `templates/cleanup-report.md`

---

### Phase 3: Intelligent Distillation / 智能提炼

**Input:** User-selected MIXED_LONG sessions
**Output:** Knowledge package → saved to `~/hermes-knowledge/`

**Steps / 提炼步骤：**
1. `hermes sessions export --session-id <ID> -` — Export full session JSON
2. Use distillation prompt (see `references/distillation-prompt.md`) to extract key info
3. Create knowledge base directory structure
4. Write extracted content

**Knowledge Base Structure / 知识库归档结构：**
```
~/hermes-knowledge/
├── {Project-Name}/
│   ├── meta.json           ← Source session metadata (ID, time, model)
│   ├── summary.md          ← Session summary (what was done, problems solved)
│   ├── decisions.md        ← Key decisions (why A over B)
│   ├── code-snippets/      ← Valuable code fragments
│   └── architecture.md     ← Architecture decisions (system design, data flow)
├── index.json              ← Global searchable index
```

**Required output / 提炼必须包含：**
- ✅ Project/Feature name
- ✅ Key decisions with rationale (at least 3)
- ✅ Code snippets (if any)
- ✅ Architecture/design decisions (if any)
- ✅ Config/parameters (if any)
- ❌ No chit-chat or filler content
- ❌ No raw conversation transcripts

**Full prompt in `references/distillation-prompt.md`** | **Template in `templates/knowledge-base/`**

---

### Phase 4: Safe Cleanup / 安全清理

**Input:** User-confirmed cleanup list
**Output:** Deleted sessions + freed disk space

**Safety mechanisms / 四级防护：**
1. **Whitelist** — 🔒 sessions never enter delete list
2. **Double confirm** — Show "Delete N sessions (X MB)?" before executing
3. **Backup safety net** — Recommend `hermes backup` for >5 sessions
4. **Batch delete** — Max 10 at a time, confirm each batch

**Execution / 执行步骤：**
```bash
# Optional: backup first
hermes backup -o ~/hermes-backup-$(date +%Y%m%d_%H%M%S).zip

# Confirm deletion
echo "⚠️ About to delete N sessions (X MB). This cannot be undone. Continue? (y/N)"
# User confirms → execute
hermes sessions delete --session-id <ID1>
hermes sessions delete --session-id <ID2>

# Verify
hermes sessions stats
```

---

### Phase 5: Scheduled Maintenance / 定时维护

**Input:** User agreement
**Output:** cron scheduled task

**Setup / 配置流程：**
1. Ask user preferred frequency
2. `hermes cron create` to create task
3. Verify task is active

```bash
hermes cron create \
  --schedule "0 10 * * 0" \
  --name "agent-cleaner-weekly" \
  --skills agent-cleaner-pro \
  --prompt "Run Agent Cleaner Pro phases 1-2: scan all sessions and generate a cleanup report. Do NOT execute deletion, only output the report for user review."
```

**Full guide in `references/cron-setup.md`**

---

## Token Cost Reference / 成本参考

| Operation | Input | Output | Cost (DeepSeek) |
|-----------|-------|--------|-----------------|
| Phase 1: Scan+Classify (50 sessions) | ~5K | ~1K | **~$0.00014** |
| Phase 2: Generate report | 0 (CLI) | 0 | **$0** |
| Phase 3: Distill 1 long session | ~50K | ~3K | **~$0.0011** |
| Phase 3: Distill 5 long sessions | ~250K | ~15K | **~$0.0056** |
| Phase 4: Execute deletion | 0 (CLI) | 0 | **$0** |
| Phase 5: Create cron | <1K | <1K | **~$0.00003** |
| **First full cleanup** | **~260K** | **~17K** | **~$0.007** |
| **Weekly maintenance** | **~10K** | **~2K** | **~$0.0003** |

---

## Commands Quick Reference / 命令速查

```bash
# View sessions / 查看会话
hermes sessions list --limit 200    # List all sessions
hermes sessions stats               # View statistics
hermes sessions export --session-id <ID> -   # Export a session

# Delete / 删除
hermes sessions delete --session-id <ID>     # Delete single
hermes sessions prune --older-than 30 --yes  # Batch delete old

# Backup / 备份
hermes backup -o ~/hermes-backup-<DATE>.zip

# Cron / 定时任务
hermes cron list                    # List existing
hermes cron create ...              # Create new
hermes cron remove <JOB_ID>         # Remove
```

---

## Common Pitfalls / 常见错误

1. **Silent delete / 静默删除** — Always show the report and get user confirmation before any deletion. Never auto-delete. / 任何清理必须先展示清单让用户确认。
2. **Whitelist omissions / 白名单遗漏** — Actively ask if project names/keywords should be whitelisted even if not in session titles. / 主动询问用户是否要保护项目相关会话。
3. **Incomplete distillation / 提炼不完整** — Must include: decisions, code, config, architecture. All four are required. / 必须包含决策、代码、配置、架构四要素。
4. **Size bias / 只看大小** — A small session with critical config is worth more than 20 test sessions. Judge by content, not size. / 按内容而非大小判断价值。
5. **No backup / 忘记备份** — For >5 sessions, always recommend `hermes backup` first. / 单次删除超过5个会话时建议先备份。
6. **Memory vs Session confusion** — Memory = user profile/preferences. Session = conversation history. This skill only manages sessions. / 本 Skill 只管理 session，不碰 memory。

---

## References / 参考文件

| File | Content |
|------|---------|
| `references/classification-guide.md` | 分类决策树 + 5种类型规则 / Classification decision tree |
| `references/distillation-prompt.md` | 提炼 Prompt 模板 / Distillation LLM prompt template |
| `references/cron-setup.md` | 定时维护配置 / Cron schedule setup guide |
| `templates/cleanup-report.md` | 清理报告模板 / Cleanup report template |
| `templates/knowledge-base/` | 知识库归档模板 / Knowledge base archive template |

## Verification Checklist / 验证清单

- [ ] Phase 1: All sessions scanned and classified / 所有会话已扫描分类
- [ ] Phase 2: Report grouped by type with sizes and recommendations / 报告已分组展示
- [ ] Phase 2: Whitelist (🔒) correctly marks protected sessions / 白名单正确
- [ ] Phase 3: Distillation contains decisions/code/architecture/config / 提炼内容完整
- [ ] Phase 3: Knowledge base written to ~/hermes-knowledge/ / 知识库已写入
- [ ] Phase 4: User confirmed before deletion / 用户确认后才执行
- [ ] Phase 4: Backup suggested for batch >5 / 批量删除时建议了备份
- [ ] Phase 5: Cron task created and verified active / 定时任务已激活
- [ ] Each phase shows before/after comparison / 每个阶段显示前后对比
