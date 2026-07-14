---
name: github-publish
description: |
  Agent-native skill for publishing local projects to GitHub.
  Auto-detects system proxy, scans sensitive files, handles commit/push/tag/verify.
license: MIT
compatibility: |
  - Agent: Kimi Work, Claude Code, OpenClaw, Cursor, Codex
  - OS: macOS (proxy auto-detect), Linux (manual proxy config)
  - Shell: bash/zsh
  - Requires: git ≥2.20, curl, python3
metadata:
  version: 1.0.0
  category: devops
  tags: [github, git, publish, automation, release, open-source]
---

# GitHub 开源发布 Skill

## 1. 触发条件

当用户说以下关键词时触发：
- "发布到 GitHub" / "github publish"
- "开源发布" / "上传代码到 GitHub"
- "push to GitHub" / "GitHub 发布"
- "整理成 GitHub 项目" / "发布代码到 GitHub"

## 2. 前置条件

1. 项目目录已初始化 git 仓库（`git init` 已完成）
2. 已配置 GitHub 远程仓库（`git remote add origin <url>` 或已有 origin）
3. 用户已在 GitHub 创建对应仓库（或已有仓库）

## 3. 执行流程（6 阶段）

```
Stage 1 → Stage 2 → Stage 3 → Stage 4 → Stage 5 → Stage 6
Prepare → Commit → Diagnose → Push → Tag → Verify
```

### Stage 1: 项目准备检查
- 检查 Git 仓库状态（`git status`, `git remote -v`）
- 检查必备文件：`.gitignore`, `README.md`, `LICENSE`（推荐）
- 扫描敏感文件是否被排除（`.env`, `secret`, `key`, `password`, `token`）
- 检查文档中的占位符（`YOUR_USERNAME`, `YOUR_REPO`, `placeholder`, `TODO`）
- 如有敏感文件或占位符，暂停并警告用户

### Stage 2: Git 提交整理
- 暂存所有更改：`git add -A`
- 生成语义化提交信息（默认：`chore: prepare for GitHub release`）
- 执行提交：`git commit -m "<message>"`
- 无更改则跳过此阶段

### Stage 3: 推送前网络诊断
- 检测系统代理配置（macOS 使用 `scutil --proxy`）
- 测试 GitHub API 可达性：`curl -sI https://api.github.com`
- 测试 Git 协议连接：`git ls-remote origin`
- 若 Git 超时但 API 可达 → 自动配置 Git 使用系统代理

### Stage 4: 推送代码
- 检测当前分支：`BRANCH=$(git branch --show-current)`
- 推送到远程当前分支：`git push origin "$BRANCH"`
- 处理常见推送错误（认证失败、超时、non-fast-forward、权限不足）
- 详细错误排查 → [故障排除手册](references/troubleshooting.md)

### Stage 5: 创建版本标签（可选）
- 用户指定版本号时执行：`git tag -a <version> -m "Release <version>"`
- 推送标签到远程：`git push origin <version>`

### Stage 6: 验证
- 验证远程提交：
  - 从 origin URL 提取 owner/repo：`OWNER_REPO=$(git remote get-url origin | sed -E 's/.*github.com[\/:]([^\/]+)\/([^\/]+)(\.git)?$/\1\/\2/')`
  - 调用 GitHub API：`curl https://api.github.com/repos/$OWNER_REPO/commits`
- 验证关键文件存在：`README.md`, `LICENSE` 等
- 生成验证报告（仓库 URL、最新提交、警告、下一步建议）

## 4. 输出格式

执行完成后输出：

```markdown
## ✅ GitHub 发布完成

**仓库**: https://github.com/<owner>/<repo>
**最新提交**: `<hash>` — <message>
**标签**: <tag 或 无>

### 项目准备检查
- [x] .gitignore 存在
- [x] README.md 存在
- [ ] LICENSE 缺失（可选）
- [x] 敏感文件已排除

### 警告（如有）
- 发现 N 个占位符未替换（YOUR_USERNAME 等）
- 建议下一步：创建 GitHub Release、设置 Topics
```

## 5. 依赖工具

| 工具 | 最低版本 | 用途 | 必需 |
|------|---------|------|------|
| git | ≥ 2.20 | 仓库操作 | 是 |
| curl | * | 网络测试与 API 验证 | 是 |
| python3 | ≥ 3.8 | API 响应解析 | 是 |
| gh | 最新 | GitHub CLI 高级操作 | 可选 |

## 6. 参考文档

- [故障排除手册](references/troubleshooting.md) — 6 种常见问题的诊断与修复
- [代理配置指南](references/proxy-guide.md) — macOS / Linux / Windows 代理设置详解
- [使用示例](references/examples/) — 简单发布、代理修复、首次发布完整场景
- [各平台 Skills 目录](references/platform-paths.md) — Kimi Work / Claude Code / OpenClaw / Cursor 安装路径速查
