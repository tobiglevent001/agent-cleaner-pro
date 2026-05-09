# Cron Setup Guide / 定时维护配置指南

## Overview / 概述

**Chinese:** 配置 Hermes cron 定时任务，让 Agent Cleaner Pro 每周自动扫描会话并生成清理报告。
**English:** Configure Hermes cron to let Agent Cleaner Pro automatically scan sessions and generate cleanup reports on a schedule.

---

## Setup Steps / 配置步骤

### Step 1: Create Cron Task / 创建任务

```bash
hermes cron create \
  --schedule "0 10 * * 0" \
  --name "agent-cleaner-weekly" \
  --skills agent-cleaner-pro \
  --prompt "Run Agent Cleaner Pro phases 1-2: scan all sessions and generate a cleanup report. Do NOT execute deletion, only output the report for user review."
```

**Parameters / 参数说明：**

| Flag / 参数 | Value / 值 | Description / 说明 |
|-------------|------------|-------------------|
| `--schedule` | `0 10 * * 0` | Every Sunday 10:00 / 每周日10点 |
| | `0 9 * * 1` | Every Monday 9:00 / 每周一9点 |
| | `0 8 1 * *` | 1st of month 8:00 / 每月1日8点 |
| | `every 7d` | Simple syntax: every 7 days / 每7天 |
| `--name` | `agent-cleaner-weekly` | Task name / 任务名称 |
| `--skills` | `agent-cleaner-pro` | Load this skill / 加载本 Skill |
| `--prompt` | (see above / 见上) | Task instructions / 执行指令 |

### Step 2: Verify Task / 验证任务

```bash
hermes cron list
```

Expected output / 预期输出：
```
  xxx [active]
    Name:      agent-cleaner-weekly
    Schedule:  0 10 * * 0
    Repeat:    ∞
    Next run:  2026-05-16T10:00:00+08:00
```

### Step 3: Test Run / 手动测试

```bash
hermes cron run <JOB_ID>
```

### Step 4: Manage / 管理

```bash
hermes cron pause <JOB_ID>    # Pause / 暂停
hermes cron resume <JOB_ID>   # Resume / 恢复
hermes cron remove <JOB_ID>   # Delete / 删除
```

---

## Frequency Options / 常用频率

| Schedule | Frequency / 频率 | Best for / 适用场景 |
|----------|-----------------|-------------------|
| `0 10 * * 0` | Weekly Sunday / 每周日 | Default / 默认推荐 |
| `0 10 1,15 * *` | Bi-weekly / 每两周 | Light users / 低频用户 |
| `0 10 1 * *` | Monthly / 每月 | Minimal users / 超低频 |
| `0 22 * * *` | Daily / 每天 | Heavy users (scan only, no report) / 高频用户 |

---

## Safety / 安全提醒

- Cron tasks only **scan + report**. No automatic deletion. / 只扫描+报告，不自动删除
- All deletion requires manual user confirmation. / 所有删除需用户手动确认
- Run the full Phase 1-4 workflow manually at least once before enabling cron. / 首次使用前先手动跑一次全流程
