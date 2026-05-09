# Agent Cleaner Pro — 智能体清理大师

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Hermes Agent Skill](https://img.shields.io/badge/Hermes-Agent%20Skill-blue)](https://hermes-agent.nousresearch.com)
[![GitHub](https://img.shields.io/badge/GitHub-Repository-black)](https://github.com/tobiglevent001/agent-cleaner-pro)

> **像 CCleaner 一样管理你的 AI 对话，但更智能。**  
> Classify → Distill → Archive → Clean → Repeat.  
> 智能体清理大师 — 分类 → 提炼 → 归档 → 清理 → 定时维护

---

## 📖 简介

**Agent Cleaner Pro（智能体清理大师）** 是一个 Hermes Agent Skill，帮你管理日益增长的对话会话。

核心三步闭环：**提炼精华 → 归档知识库 → 清理冗余**

不只是删对话，而是把有价值的决策、代码、配置提炼出来永久保存，把无用的垃圾清掉。

### 你是否有这些问题？

- 对话越来越多，`session_search` 越来越慢
- 怕删错东西，所以不敢清理 → 越来越臃肿
- 明明有用的内容散落在长对话里，找不到
- 做过的事情没有形成知识积累，下次还得重新问

## 🎯 核心功能

| 功能 | 说明 | 独创性 |
|------|------|--------|
| 🔍 **会话自动分类** | 5 种类型（项目/问答/混合/调试/未分类） | ✅ 独创 |
| 📋 **可视化清理清单** | CCleaner 风格，按类型分组+大小+建议操作 | ✅ 独创 |
| 💡 **智能提炼** | 从长对话中提取关键决策/代码/配置 | ✅ 独创 |
| 📦 **知识库归档** | 提炼结果打包存入本地 `~/hermes-knowledge/` | ✅ 独创 |
| 🔒 **安全清理** | 白名单保护 + 二次确认 | ✅ 独创 |
| ⏰ **定时维护** | cron 自动扫描+增量报告 | ✅ 独创 |

## 🚀 快速开始

### 安装

```bash
# 从 GitHub 安装
hermes skills install github:tobiglevent001/agent-cleaner-pro

# 或手动
git clone https://github.com/tobiglevent001/agent-cleaner-pro.git ~/.hermes/skills/software-development/agent-cleaner-pro/
```

### 使用

在 Hermes Agent 中直接说：

```bash
🧹 小马，帮我清理一下对话
🧹 智能体清理大师，扫描一下我的会话
```

## 📁 项目结构

```
agent-cleaner-pro/
├── SKILL.md                              ← 主 Skill（工作流入口）
├── README.md                             ← 本文件
├── references/
│   ├── classification-guide.md           ← 分类决策树
│   ├── distillation-prompt.md            ← 提炼 Prompt 模板
│   └── cron-setup.md                     ← 定时维护配置指南
└── templates/
    ├── cleanup-report.md                 ← 清理报告模板
    └── knowledge-base/
        └── summary.md                    ← 知识库条目模板
```

## 📊 会话分类

| 类型 | 代码 | 特征 | 默认策略 |
|------|------|------|---------|
| 📁 长期项目 | `PROJECT` | 标题含项目名、轮数>20、含代码/架构讨论 | 永久保留 |
| 🗑️ 一次性问答 | `QUICK_QA` | 简单问题、轮数<5、无代码 | 7天后删除 |
| 🧩 超长混合 | `MIXED_LONG` | 轮数>20、混合多种话题 | 提炼后删除 |
| 📋 配置调试 | `CONFIG_DEBUG` | 配置/调试/切换关键词 | 30天后删除 |
| ❓ 未分类 | `UNKNOWN` | 无明确特征 | 扫描后人工判断 |

## 💰 Token 成本

| 操作 | 成本 (DeepSeek) |
|------|----------------|
| 扫描+全部分类 (50会话) | ~¥0.001 |
| 生成清理清单 | ¥0 (纯 CLI) |
| 提炼 1 个长会话 | ~¥0.008 |
| **首次全量清理 (含提炼)** | **~¥0.05** |
| 每周维护 | ~¥0.002 |

## 🗺️ 路线图

| 阶段 | 目标 | 状态 |
|------|------|------|
| **Phase 1** 🆓 | 开源发布，核心功能可用 | ✅ 已完成 |
| **Phase 2** 🆓 | 社区反馈迭代，Star 50+ | ⏳ 进行中 |
| **Phase 3** 💰 | 高级功能付费版（无限提炼+知识库） | 📅 规划中 |
| **Phase 4** 💰 | 企业版（Web UI + 团队共享） | 📅 规划中 |

## 📄 License

MIT
