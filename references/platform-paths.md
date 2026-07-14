# 各 Agent Skills 目录速查

## 快速查找表

| Agent | macOS 路径 | Windows 路径 | Linux 路径 |
|-------|-----------|-------------|-----------|
| **Kimi Work** | `~/Library/Application Support/kimi-desktop/daimon/skills/` | `%APPDATA%\kimi-desktop\daimon\skills\` | `~/.config/kimi-desktop/daimon/skills/` |
| **Claude Code** | `~/.claude/skills/` | `~/.claude/skills/`（WSL/Git Bash） | `~/.claude/skills/` |
| **OpenClaw** | `~/.openclaw/skills/` | `~/.openclaw/skills/`（WSL/Git Bash） | `~/.openclaw/skills/` |
| **Cursor** | `~/.cursor/skills/` | `~/.cursor/skills/`（WSL/Git Bash） | `~/.cursor/skills/` |
| **Codex** | `~/.codex/skills/` | `~/.codex/skills/`（WSL/Git Bash） | `~/.codex/skills/` |

> 注：Windows 路径中 `~` 表示用户主目录（`C:\Users\<用户名>`）。部分 Agent 在 Windows 上推荐通过 WSL 使用，此时路径为 Linux 格式。

---

## 各平台详细安装命令

### Kimi Work

**macOS**：
```bash
# 克隆到 Kimi Work skills 目录
git clone https://github.com/zhongwentuo-creator/github-publish-skill.git \
  ~/Library/Application\ Support/kimi-desktop/daimon/skills/github-publish
```

**Windows**：
```cmd
# CMD
git clone https://github.com/zhongwentuo-creator/github-publish-skill.git ^
  %APPDATA%\kimi-desktop\daimon\skills\github-publish

# PowerShell
git clone https://github.com/zhongwentuo-creator/github-publish-skill.git `
  "$env:APPDATA\kimi-desktop\daimon\skills\github-publish"
```

**Linux**：
```bash
git clone https://github.com/zhongwentuo-creator/github-publish-skill.git \
  ~/.config/kimi-desktop/daimon/skills/github-publish
```

---

### Claude Code

**macOS / Linux**：
```bash
git clone https://github.com/zhongwentuo-creator/github-publish-skill.git \
  ~/.claude/skills/github-publish
```

**Windows**（通过 Git Bash 或 WSL）：
```bash
git clone https://github.com/zhongwentuo-creator/github-publish-skill.git \
  ~/.claude/skills/github-publish
```

> Claude Code 的 Skills 功能需 CLI 版本 ≥ 0.2.0。启用方式：
> ```bash
> claude config set enableSkills true
> ```

---

### OpenClaw

**macOS / Linux**：
```bash
git clone https://github.com/zhongwentuo-creator/github-publish-skill.git \
  ~/.openclaw/skills/github-publish
```

**Windows**（通过 Git Bash 或 WSL）：
```bash
git clone https://github.com/zhongwentuo-creator/github-publish-skill.git \
  ~/.openclaw/skills/github-publish
```

---

### Cursor

**macOS**：
```bash
git clone https://github.com/zhongwentuo-creator/github-publish-skill.git \
  ~/.cursor/skills/github-publish
```

**Windows / Linux**：
> Cursor 目前对自定义 Skills 支持有限。建议通过 `.cursorrules` 文件引入核心指令，或关注 Cursor 官方更新。

---

### Codex

**macOS / Linux**：
```bash
git clone https://github.com/zhongwentuo-creator/github-publish-skill.git \
  ~/.codex/skills/github-publish
```

**Windows**（通过 Git Bash 或 WSL）：
```bash
git clone https://github.com/zhongwentuo-creator/github-publish-skill.git \
  ~/.codex/skills/github-publish
```

> Codex CLI 的 Skills 功能需通过 `--skill` 参数显式加载。

---

## 安装后验证

安装完成后，验证 Skill 是否正确加载：

```bash
# 检查文件是否存在
ls <agent-skills-path>/github-publish/SKILL.md

# 对 Agent 说以下测试语句
"发布这个项目到 GitHub"
"帮我检查 GitHub 发布状态"
"我的项目能发布到 GitHub 吗"
```

若 Agent 正确识别并执行 Git 相关检查，说明 Skill 安装成功。
