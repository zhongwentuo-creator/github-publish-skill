# GitHub Publish Skill

> 面向 Agent 的本地项目自动发布到 GitHub 开源仓库 Skill。内置零配置系统代理检测。

## 特性

- **自动检测系统代理**（macOS Clash/V2Ray/Surge）
- **推送前扫描敏感文件**（`.env`、密钥、令牌等）
- **自动生成语义化提交信息**
- **自动创建和推送版本标签**
- **通过 GitHub API 验证远程仓库**
- **多平台支持**：Kimi Work、Claude Code、OpenClaw、Cursor、Codex

## 快速开始

### 安装

**方式一：一键安装**

对你的 Agent 说：

```
安装这个 skill: https://github.com/zhongwentuo-creator/github-publish-skill
```

**方式二：Agent 命令**

对 Kimi Work、Claude Code、OpenClaw、Cursor 等 Agent，使用其原生安装命令：

```
安装这个 skill: https://github.com/zhongwentuo-creator/github-publish-skill
```

> **注意：** 不同 Agent 的安装命令可能不同，详见 [references/platform-paths.md](references/platform-paths.md)。

**方式三：手动安装**

```bash
git clone https://github.com/zhongwentuo-creator/github-publish-skill.git
cp -r github-publish-skill ~/.kimi/daimon/skills/
# 或: ~/.claude/skills/, ~/.openclaw/skills/, ~/.cursor/skills/
```

各平台安装路径详见 [references/platform-paths.md](references/platform-paths.md)。

### 使用

直接对你的 Agent 说：

```
发布这个项目到 GitHub
```

或带参数：

```
发布这个项目到 GitHub，标签 v1.0.0
```

## 环境要求

- `git` ≥ 2.20
- `curl`
- `python3` ≥ 3.8
- 已配置 GitHub 账号（SSH 或 HTTPS 认证）

## 工作原理

```
准备检查 → Git 提交 → 网络诊断 → 推送 → 标签（可选）→ 验证
```

1. **准备检查** — 验证 git 仓库、远程 origin、必备文件、敏感文件、占位符
2. **Git 提交** — 暂存更改并使用语义化信息提交
3. **网络诊断** — 检测系统代理，测试 GitHub API 和 Git 协议连通性
4. **推送** — 推送到远程 main 分支，需要时自动修复代理配置
5. **标签** — 如用户请求，创建并推送版本标签
6. **验证** — 通过 GitHub API 确认远程提交并生成验证报告

详见 [SKILL.md](SKILL.md) 获取完整流程说明。

## 安全声明

⚠️ 本 Skill 会在你的授权下执行以下命令：

| 命令 | 用途 | 执行阶段 |
|------|------|----------|
| `git status` / `git log` / `git remote` | 检查仓库状态 | 阶段 1 |
| `git add` / `git commit` | 暂存并提交更改 | 阶段 2 |
| `curl` | 测试 GitHub API 连通性 | 阶段 3 |
| `scutil` | 自动检测 macOS 系统代理 | 阶段 3（仅限 macOS）|
| `git push` / `git tag` | 推送代码和标签到远程 | 阶段 4–5 |
| `python3` | 解析 API 响应、代理检测 | 阶段 3、6 |

**安全保证：**
- **不会**读取或修改包含真实密钥的 `.env` 文件
- **会**扫描敏感文件并在推送前警告你
- **不会**执行上述列表之外的任何命令
- **会**在修复代理或推送前请求确认

## 使用示例

详见 [references/examples/](references/examples/) 目录：

- [示例 1：简单项目发布](references/examples/example-01-simple.md)
- [示例 2：代理自动检测和修复](references/examples/example-02-with-proxy.md)
- [示例 3：首次发布完整流程（含标签）](references/examples/example-03-first-release.md)

## 文档

- [SKILL.md](SKILL.md) — 核心 Skill 文件，包含完整工作流程
- [references/troubleshooting.md](references/troubleshooting.md) — 6 个常见问题的故障排除指南
- [references/proxy-guide.md](references/proxy-guide.md) — macOS/Linux/Windows 代理配置详解
- [references/platform-paths.md](references/platform-paths.md) — 各 Agent 的 Skills 目录速查表

## 贡献指南

欢迎贡献！详见 [CONTRIBUTING.md](CONTRIBUTING.md)。

## 更新日志

详见 [CHANGELOG.md](CHANGELOG.md)。

## 许可证

[MIT](LICENSE) © 2026 github-publish-skill Contributors
