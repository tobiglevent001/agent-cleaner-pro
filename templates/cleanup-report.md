# Cleanup Report Template / 清理报告模板

## Template / 模板

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  🧹 Agent Cleaner Pro — Cleanup Report / 对话清理报告
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  Generated / 生成时间: {YYYY-MM-DD HH:mm}
  Current state / 当前状态: {N} sessions, {N} messages, {N}MB

📁 PROJECTS (Keep Forever / 建议永久保留)         {N}   {X}MB
{One line per session / 每个会话一行:
  [🔒] {Session Title}                     {X}MB  🔒Forever
}

📋 CONFIG/DEBUG (Clean after 30d / 30天后可清理)  {N}   {X}MB
{One line per session:
  [☐] {Session Title}                     {X}MB  ⚡Keep until {YYYY-MM-DD}
}

🗑️ QUICK Q&A (Clean now / 建议立即清理)           {N}   {X}MB
{One line per session:
  [☐] {Session Title}                     {X}MB  ⚡{N} days ago
}

🧩 MIXED LONG (Distill then clean / 需提炼后清理)  {N}   {X}MB
{One line per session:
  [🔍] {Session Title}                     {X}MB  → Ready to distill
}

❓ UNKNOWN (Pending classification / 待确认)      {N}   {X}MB
{One line per session:
  [?] {Session Title/Preview}              {X}MB  → Need classification
}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 Summary / 汇总:
  Keep Forever / 永久保留:    {N} sessions ({X}MB)  🔒
  To Distill / 待提炼:        {N} sessions ({X}MB)  🔍
  Can Clean / 可清理:         {N} sessions ({X}MB)  ⚡
  Unknown / 未分类:           {N} sessions ({X}MB)  ❓
  ──────────────────────────────
  Freed after cleanup / 清理后可释放: ~{X}MB / {X}MB total ({X}%)

Options / 操作:
  [1] Clean selected / 清理勾选内容
  [2] Distill + archive + clean / 提炼+归档+清理
  [3] View session details / 查看详情
  [4] Backup then clean / 先备份再清理
  [5] Cancel / 取消
```

## Instructions / 使用说明

1. Replace `{placeholders}` with actual scan data / 用实际数据替换占位符
2. 🔒 sessions have no delete option / 🔒 标记不生成删除选项
3. Recommended deletions are pre-checked ([☐]) / 建议删除的默认勾选
4. User selects an option → execute corresponding action / 用户选择后执行对应操作
