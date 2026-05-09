# Cron Setup Guide — 定时维护配置指南

## Overview

配置 Hermes cron 定时任务，让 Agent Cleaner Pro 每周自动扫描会话并生成清理报告。

## 配置步骤

### 步骤1：创建 cron 任务

```bash
hermes cron create \
  --schedule "0 10 * * 0" \
  --name "agent-cleaner-weekly" \
  --skills agent-cleaner-pro \
  --prompt "执行智能体清理大师的阶段1-2：扫描所有会话并生成清理报告。不要执行删除，只输出报告供用户查看。"
```

**参数说明：**
| 参数 | 值 | 说明 |
|------|------|------|
| `--schedule` | `0 10 * * 0` | cron 表达式：每周日 10:00 |
| | `0 9 * * 1` | 每周一 9:00 |
| | `0 8 1 * *` | 每月1日 8:00 |
| | `0 18 * * 5` | 每周五 18:00（下班前扫一遍） |
| | `every 7d` | 简单语法：每7天 |
| `--name` | `agent-cleaner-weekly` | 任务名称，便于管理 |
| `--skills` | `agent-cleaner-pro` | 加载本 Skill |
| `--prompt` | (见上) | 任务执行的指令 |

### 步骤2：验证任务

```bash
hermes cron list
```

确认输出包含：
```
  xxx [active]
    Name:      agent-cleaner-weekly
    Schedule:  0 10 * * 0
    Repeat:    ∞
    Next run:  2026-05-16T10:00:00+08:00
```

### 步骤3：手动测试执行

```bash
hermes cron run <JOB_ID>
```

### 步骤4：如有需要，暂停/恢复/删除

```bash
hermes cron pause <JOB_ID>    # 暂停
hermes cron resume <JOB_ID>   # 恢复
hermes cron remove <JOB_ID>   # 删除
```

## 常用频率配置

| 频率 | Schedule | 适用场景 |
|------|----------|---------|
| 每周日 | `0 10 * * 0` | 默认推荐（周日醒来看看） |
| 每两周 | `0 10 1,15 * *` | 低频用户 |
| 每月1号 | `0 10 1 * *` | 超低频用户 |
| 每天 | `0 22 * * *` | 高频用户（建议只扫描不出报告） |

## 安全提醒

- cron 任务只做**扫描+报告**，不做自动删除
- 所有删除操作都需要用户手动确认
- 首次执行前，建议用户先手动运行一次完整的阶段1-4体验流程
