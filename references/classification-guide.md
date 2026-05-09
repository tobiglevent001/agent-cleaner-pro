# Classification Guide / 分类决策手册

## Overview / 概述

**Chinese:** 每个会话从 3 个维度评估：标题语义、对话规模、内容特征
**English:** Each session is evaluated on 3 dimensions: title semantics, conversation scale, and content features.

| Dimension / 维度 | Source / 数据来源 | Description / 说明 |
|------------------|-------------------|-------------------|
| Title / 标题语义 | `hermes sessions list` Title field | 会话标题或"—" |
| Scale / 对话规模 | `hermes sessions export` message_count | 轮数、Token 数 |
| Content / 内容特征 | Preview + first message | Keywords / 关键词匹配 |

---

## Classification Decision Tree / 五类分类决策树

### Type A: 📁 PROJECT (Long-term Project / 长期项目)

**Criteria / 识别条件（满足任一即可）：**
```
Title contains: 开发/系统/项目/复刻/复现/搭建/架构/Skill
            OR 网站/平台/GEO/监测/monitor(ing)
            OR 项目名（如 Smart Model Selector）/ project name
Rounds: message_count >= 15
        AND
Content: 代码/架构/设计/方案/code/architecture/design
```

**Examples / 典型例子：**
- "Smart Model Selector Skill Review" — 188 msgs, project development
- "网站复刻能力分析" — code + architecture discussion
- "GEO监测网站开发" — system build

**Action / 建议：** 🔒 Keep forever / 永久保留
**User decision / 用户决策：** "Project still active" → Forever. "Project abandoned" → Distill then delete.

### Type B: 🗑️ QUICK_QA (One-time Q&A / 一次性问答)

**Criteria / 识别条件：**
```
Title contains: 天气/多少/怎么/能不能/可以吗/是什么/weather/how/can you/what is
            OR Preview: Simple question (<20 chars)
Rounds: message_count < 8
        AND
Content: No code blocks, no architecture discussion
```

**Examples / 典型例子：**
- "明天上海什么天气" / "Weather in Shanghai tomorrow" — 1 round
- "你可以接入我的IMA数据吗" / "Can you connect to my IMA data" — 3 rounds
- "1+9等于多少" / "What is 1+9" — 1 round

**Action / 建议：** ⚡ Clean now / 立即清理 or Keep 7 days then clean
**Safety / 安全提醒：** Check if a QA session unexpectedly contains valuable follow-up content.

### Type C: 🧩 MIXED_LONG (Mixed Long Session / 超长混合会话)

**Criteria / 识别条件：**
```
Rounds: message_count >= 20
        AND
Content: Topic switches >3 times / 话题切换3次以上
        OR Contains code + decisions + chit-chat
```

**Examples / 典型例子：**
- 50 rounds: first 10 config, middle 20 dev, last 20 bug fixing
- 30 rounds: starts with model questions → coding → architecture discussion

**Action / 建议：** 🔍 Distill + archive first → then delete original / 先提炼归档再删除

### Type D: 📋 CONFIG_DEBUG (Configuration/Debugging / 配置调试)

**Criteria / 识别条件：**
```
Title contains: 配置/调试/升级/切换/安装/报错/config/debug/upgrade
            OR 错误/修复/解决/问题/同步/连接/error/fix/issue/sync
            OR hermes doctor/upgrade/update/setup
```

**Examples / 典型例子：**
- "CLI与Web UI模型同步问题" / "CLI-Web UI sync issue"
- "Hermes v0.13 Upgrade Guide"
- "设置模型上下文长度" / "Setting context length"

**Action / 建议：** 📋 Keep 30 days, delete after system stability confirmed
**Exception / 例外：** If the session contains valuable config parameters, archive before deletion.

### Type E: ❓ UNKNOWN (Unclassified / 未分类)

**Criteria / 识别条件：** Does not match any of the above 4 types, or title is "—" with unclear preview.

**Process / 处理流程：**
```
1. Read first user message for clues
2. If still unclear → ask user: "What was this session about?"
3. Reclassify based on user feedback
```

**Action / 建议：** ⏳ Keep temporarily, classify after user feedback

---

## Special Scenarios / 特殊场景

### Scenario 1: Untitled Sessions ("—")

Hermes untitled sessions usually come from:
- Quick Q&A (title not yet generated)
- First-time testing / 首次安装测试
- Short CLI interactions

**Strategy / 处理策略：**
```
Check preview →
  "can you/hello/first time/能用吗/可以吗/你是/你好/第一次" → QUICK_QA
  "hermes doctor/config/debug" → CONFIG_DEBUG
  "code/write/create/写一个/实现/开发" → Read more, likely PROJECT
  Other → UNKNOWN, ask user
```

### Scenario 2: Same Project, Multiple Sessions

A project may have multiple sessions (e.g., "Smart Model Selector" repeated).
**Handle / 处理：** Merge distillation into one knowledge base entry, delete originals.

### Scenario 3: Config Parameters in QA Sessions

User might reveal important config preferences in a seemingly simple QA session.
**Handle / 处理：** Check for substantive content. If found → distill + archive → delete original.

---

## Confidence Score / 分类置信度

| Score | Meaning / 含义 | Action / 处理 |
|-------|---------------|--------------|
| High (>80%) | Clear classification / 分类明确 | Auto-classify, show in report |
| Medium (50-80%) | Some ambiguity / 有模糊空间 | Mark "suggested" in report, let user confirm |
| Low (<50%) | Can't determine / 无法判断 | Mark UNKNOWN, ask user |
