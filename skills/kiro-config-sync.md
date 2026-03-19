---
inclusion: manual
---

# Kiro IDE 与 kiro-cli 配置同步指南

当需要让 Kiro IDE 和 kiro-cli 共享 skills、steering、MCP 等配置时，按以下规则操作。

## 核心原理

两者都读取 `~/.kiro/` 目录，但 skills 的文件结构要求不同：
- Kiro IDE 读取：`~/.kiro/skills/*.md`（扁平文件）
- kiro-cli 读取：`~/.kiro/skills/*/SKILL.md`（子目录结构）

## 统一方案

对每个 skill，采用"子目录放真实文件 + 符号链接兼容 IDE"的方式：

```
~/.kiro/skills/
├── my-skill/SKILL.md          ← 真实文件（CLI 和 IDE 共用）
└── my-skill.md → my-skill/SKILL.md  ← 符号链接（IDE 兼容）
```

## 操作步骤

### 新增 skill
```bash
SKILL_NAME="my-new-skill"
mkdir -p ~/.kiro/skills/$SKILL_NAME
# 编辑 ~/.kiro/skills/$SKILL_NAME/SKILL.md 写入内容
ln -sf ~/.kiro/skills/$SKILL_NAME/SKILL.md ~/.kiro/skills/$SKILL_NAME.md
```

### 从扁平文件迁移到兼容结构
```bash
SKILL_NAME="existing-skill"
mkdir -p ~/.kiro/skills/$SKILL_NAME
mv ~/.kiro/skills/$SKILL_NAME.md ~/.kiro/skills/$SKILL_NAME/SKILL.md
ln -s ~/.kiro/skills/$SKILL_NAME/SKILL.md ~/.kiro/skills/$SKILL_NAME.md
```

## 其他共享配置

以下配置天然共享，无需额外处理：
- `~/.kiro/settings/mcp.json` — MCP 服务器配置
- `~/.kiro/steering/*.md` — 用户级 steering 规则
- `~/.kiro/agents/` — Agent 配置

## 同步到 GitHub

本地 skills 可通过 `~/.kiro/sync-skills.sh` 脚本自动同步到 GitHub 仓库备份。
