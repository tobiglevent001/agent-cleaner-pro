# Distillation Prompt / 提炼 Prompt 模板

## Overview / 概述

**Chinese:** 从长对话中提炼有价值信息时使用的 Prompt 模板。适用于 MIXED_LONG 类型会话。
**English:** LLM prompt template for extracting valuable information from long conversations. Use for MIXED_LONG sessions.

**Input source / 输入源：** `hermes sessions export --session-id <ID> -`

---

## LLM Prompt Template

```
You are a Conversation Essence Extractor. Your task is to extract all valuable
information from an AI assistant conversation and output it as structured knowledge.

You are also capable of reading and processing Chinese content. If the conversation
is in Chinese, respond in Chinese. If English, respond in English.

【INPUT / 输入】
Below is the complete conversation (JSON format) with N rounds:

{exported session JSON}

【OUTPUT / 输出要求】
Extract and output ONLY the following sections. Do NOT output the original conversation.

## 1. Project/Feature Name / 项目/功能名称
(Brief description of what this conversation accomplished)

## 2. Session Summary / 会话概要
(2-3 sentences: context, problem to solve, final outcome)

## 3. Key Decisions / 关键决策记录
| Decision / 决策 | Choice / 方案 | Rationale / 理由 | Impact / 影响 |
|---|---|---|---|
| e.g. Frontend framework | Vue3 + Element Plus | User familiar, good CN docs, rich components | Foundation for all future dev |
| ... | ... | ... | ... |

List at least 3. If none, say "No major decisions in this session."

## 4. Valuable Code Snippets / 有价值的代码片段
```language
# filename / purpose
code content
```
Only include code that has long-term reference value.
No temporary debug code or meaningless examples.

## 5. Architecture/Design Decisions / 架构设计决策
(System structure, data flow, component relationships, etc.)
If none, say "No architecture decisions in this session."

## 6. Configuration/Parameters / 配置参数
(Any useful config values, parameters, API key hints, etc.)
If none, say "No configuration in this session."

## 7. Lessons Learned / 经验教训
(Any pitfalls, bugs, or hard-earned lessons from this session)
If none, say "No lessons learned in this session."

【Quality Requirements / 质量要求】
- Accurate: Only extract content that actually exists in the conversation
- Concise: Max 200 words per section (code excluded)
- Complete: Don't miss any long-term valuable information
- If the entire conversation has no value, output: "This session has no long-term retention value."
```

---

## Post-processing / 后处理

After distillation, write results to knowledge base:

```
~/hermes-knowledge/{Project-Name}/
├── summary.md        ← Summary + key decisions / 概要+关键决策
├── code-snippets/    ← Code / 代码片段
├── architecture.md   ← Architecture / 架构决策
└── meta.json         ← Source metadata / 源元数据
```

## Quality Check / 质量验证

- [ ] Project name is clear and self-explanatory / 项目名称一目了然
- [ ] Decisions include "what was chosen" AND "why" / 决策包含"选了什么"和"为什么选"
- [ ] Code snippets are independently usable / 代码片段可独立使用
- [ ] No chit-chat, pleasantries, or failed attempts included / 无闲聊内容
- [ ] Total output < 10% of original conversation size / 总字数不超过原文10%
