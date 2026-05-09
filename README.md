# Agent Cleaner Pro — 智能体清理大师

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Hermes Agent Skill](https://img.shields.io/badge/Hermes-Agent%20Skill-blue)](https://hermes-agent.nousresearch.com)
[![GitHub](https://img.shields.io/badge/GitHub-Repository-black)](https://github.com/tobiglevent001/agent-cleaner-pro)

> **像 CCleaner 一样管理你的 AI 对话，但更智能。**  
> Classify → Distill → Archive → Clean → Repeat.  
> 智能体清理大师 — 分类 → 提炼 → 归档 → 清理 → 定时维护

---

## 📖 简介

**Agent Cleaner Pro（智能体清理大师）** 是一个 Hermes Agent Skill，帮你管理日益增长的对话会话。核心思想：

> **不是单纯"删对话"，而是"提炼精华 → 归档知识库 → 清理冗余"的三步闭环。**

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

# 或者手动复制
cp SKILL.md ~/.hermes/skills/software-development/agent-cleaner-pro/
```

### 使用

在 Hermes Agent 中直接说：

```bash
# 中文
🧹 小马，帮我清理一下对话
🧹 智能体清理大师，扫描一下我的会话

# English
🧹 Hey, clean up my conversation sessions
```

你会看到一份清理报告，然后选择操作即可。

### 高级用法

```bash
# 查看当前会话统计
hermes sessions stats

# 仅扫描，不执行任何操作
小马，先扫描一下我的对话，看看哪些需要清理

# 设置每周自动检查（通过 cron）
小马，帮我设置每周日自动检查对话清理

# 提炼某个重要对话到知识库
小马，把"GEO监测系统"这个对话提炼出来保存到知识库
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
| 扫描+全部分类 | ~¥0.001 |
| 生成清理清单 | ¥0 (纯 CLI) |
| 提炼 1 个长会话 | ~¥0.008 |
| **首次全量清理** | **~¥0.05 (≈$0.007)** |
| 每周维护 | ~¥0.002 |

## 🗺️ 路线图

| 阶段 | 目标 | 状态 |
|------|------|------|
| **Phase 1** 🆓 | 开源发布，核心功能可用 | ✅ 已完成 |
| **Phase 2** 🆓 | 社区反馈迭代，Star 50+ | ⏳ 进行中 |
| **Phase 3** 💰 | 高级功能付费版（无限提炼+知识库） | 📅 规划中 |
| **Phase 4** 💰 | 企业版（Web UI + 团队共享） | 📅 规划中 |

## 📄 License

MIT — 开源免费，欢迎贡献！

## 🤝 如何贡献

1. Fork 本仓库
2. 创建你的特性分支 (`git checkout -b feature/amazing-feature`)
3. 提交修改 (`git commit -m 'Add amazing feature'`)
4. 推送到分支 (`git push origin feature/amazing-feature`)
5. 创建 Pull Request

## 📞 联系方式

- GitHub Issues: [提交问题](https://github.com/tobiglevent001/agent-cleaner-pro/issues)
- GitHub Discussions: [参与讨论](https://github.com/tobiglevent001/agent-cleaner-pro/discussions)
