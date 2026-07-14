# GitHub Publish Skill

> Agent-native skill for publishing local projects to GitHub with zero-config proxy detection.

## Features

- **Auto-detect system proxy** (macOS Clash/V2Ray/Surge)
- **Scan sensitive files** before push (`.env`, secrets, keys, tokens)
- **Auto git commit** with semantic messages
- **Create and push version tags** automatically
- **Verify remote repository** after push via GitHub API
- **Multi-platform support**: Kimi Work, Claude Code, OpenClaw, Cursor, Codex

## Quick Start

### Install

**Option 1: One-click install**

Say to your agent:

```
Install this skill: https://github.com/zhongwentuo-creator/github-publish-skill
```

**Option 2: Agent Command**

For Kimi Work, Claude Code, OpenClaw, Cursor, etc., use your agent's native install command:

```
Install this skill: https://github.com/zhongwentuo-creator/github-publish-skill
```

> **Note:** Different agents may have different install commands. See [references/platform-paths.md](references/platform-paths.md) for platform-specific details.

**Option 3: Manual**

```bash
git clone https://github.com/zhongwentuo-creator/github-publish-skill.git
cp -r github-publish-skill ~/.kimi/daimon/skills/
# Or: ~/.claude/skills/, ~/.openclaw/skills/, ~/.cursor/skills/
```

See [references/platform-paths.md](references/platform-paths.md) for the exact skills directory on your platform.

### Usage

Just say to your agent:

```
Publish this project to GitHub
```

Or with options:

```
Publish this project to GitHub with tag v1.0.0
```

## Requirements

- `git` ≥ 2.20
- `curl`
- `python3` ≥ 3.8
- GitHub account with SSH or HTTPS auth configured

## How It Works

```
┌─────────────┐   ┌─────────────┐   ┌─────────────┐   ┌─────────────┐   ┌─────────────┐   ┌─────────────┐
│ Prepare     │ → │ Git Commit  │ → │ Network     │ → │ Push        │ → │ Tag         │ → │ Verify      │
│ Check       │   │             │   │ Diagnose    │   │             │   │ (optional)  │   │             │
└─────────────┘   └─────────────┘   └─────────────┘   └─────────────┘   └─────────────┘   └─────────────┘
```

1. **Prepare Check** — Verify git repo, remote origin, required files, sensitive files, placeholders
2. **Git Commit** — Stage changes and commit with semantic messages
3. **Network Diagnose** — Detect system proxy, test GitHub API and Git protocol connectivity
4. **Push** — Push to remote main branch with auto proxy fix if needed
5. **Tag** — Create and push version tag if requested
6. **Verify** — Confirm remote commit via GitHub API and generate report

See [SKILL.md](SKILL.md) for the full workflow details.

## Safety

⚠️ This skill executes the following commands on your behalf:

| Command | Purpose | When |
|---------|---------|------|
| `git status` / `git log` / `git remote` | Check repository state | Stage 1 |
| `git add` / `git commit` | Stage and commit changes | Stage 2 |
| `curl` | Test GitHub API connectivity | Stage 3 |
| `scutil` | Auto-detect macOS system proxy | Stage 3 (macOS only) |
| `git push` / `git tag` | Push code and tags to remote | Stage 4–5 |
| `python3` | Parse API responses, proxy detection | Stage 3, 6 |

**Safety guarantees:**
- It will **NOT** read or modify any `.env` files containing real secrets
- It will **scan** for sensitive files and warn you before push
- It will **NOT** execute any command not listed above
- It will **ask for confirmation** before fixing proxy or pushing

## Examples

See [references/examples/](references/examples/) for detailed usage scenarios:

- [Example 1: Simple project publish](references/examples/example-01-simple.md)
- [Example 2: Auto proxy detection and fix](references/examples/example-02-with-proxy.md)
- [Example 3: First release with tag](references/examples/example-03-first-release.md)

## Documentation

- [SKILL.md](SKILL.md) — Core skill file with full workflow
- [references/troubleshooting.md](references/troubleshooting.md) — Troubleshooting guide for 6 common issues
- [references/proxy-guide.md](references/proxy-guide.md) — macOS/Linux/Windows proxy configuration
- [references/platform-paths.md](references/platform-paths.md) — Skills directory paths for all supported agents

## Contributing

We welcome contributions! See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

## Changelog

See [CHANGELOG.md](CHANGELOG.md) for version history.

## License

[MIT](LICENSE) © 2026 github-publish-skill Contributors
