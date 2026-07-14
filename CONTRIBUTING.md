# Contributing to GitHub Publish Skill

Thank you for your interest in contributing! This document provides guidelines for reporting issues, submitting pull requests, and testing your changes.

## How to Contribute

### Reporting Issues

Before opening a new issue, please:

1. **Search existing issues** to avoid duplicates.
2. **Include the following information** in your bug report:
   - **Agent name** (e.g., Kimi Work, Claude Code, OpenClaw, Cursor, Codex)
   - **Operating system** and version (e.g., macOS 14.5, Ubuntu 22.04)
   - **Shell** (e.g., bash, zsh, fish)
   - **Git version** (`git --version`)
   - **Error output** or a description of unexpected behavior
   - **Steps to reproduce** the issue

Issue templates are encouraged but not strictly required.

### Submitting Pull Requests

1. **Fork** the repository on GitHub.
2. **Clone** your fork locally.
3. **Create a feature branch**:
   ```bash
   git checkout -b feature/your-feature-name
   ```
4. **Make your changes** and ensure they follow the existing style and conventions.
5. **Test your changes** using the steps below.
6. **Update `CHANGELOG.md`** under the `[Unreleased]` section (or create a new version section if appropriate).
7. **Commit** with a clear, descriptive message:
   ```bash
   git commit -m "feat: add Windows proxy auto-detection"
   ```
8. **Push** to your fork and **open a Pull Request** against the `main` branch.
9. **Describe** what your PR does and why it is needed.

### Skill Testing

Before submitting a PR, please test your changes with a real agent environment:

#### Step 1: Install the skill locally

```bash
# Clone your fork
git clone https://github.com/YOUR_USERNAME/github-publish-skill.git

# Copy to your agent's skills directory
# For Kimi Work (macOS):
cp -r github-publish-skill ~/Library/Application\ Support/kimi-desktop/daimon/skills/github-publish

# For Claude Code:
cp -r github-publish-skill ~/.claude/skills/github-publish

# For OpenClaw:
cp -r github-publish-skill ~/.openclaw/skills/github-publish

# For Cursor:
cp -r github-publish-skill ~/.cursor/skills/github-publish
```

#### Step 2: Create a test repository

```bash
mkdir /tmp/test-repo
cd /tmp/test-repo
git init
git remote add origin https://github.com/YOUR_USERNAME/test-repo.git
```

#### Step 3: Trigger the skill

Speak to your agent (e.g.):

> "Publish this project to GitHub"

or

> "发布这个项目到 GitHub"

#### Step 4: Verify the output

Confirm the agent:
- Detects the repository correctly
- Handles any proxy issues (if applicable)
- Commits and pushes successfully
- Generates a verification report

### Code Style

- **Markdown**: Use standard GitHub-flavored Markdown.
- **SKILL.md**: Keep the main file under 200 lines; move detailed content to `references/`.
- **YAML frontmatter**: Follow the Moonshot Agent Skills specification.
- **JSON**: Use 2-space indentation in `skill.json`.

### Commit Message Convention

We follow the [Conventional Commits](https://www.conventionalcommits.org/) specification:

- `feat:` — A new feature
- `fix:` — A bug fix
- `docs:` — Documentation only changes
- `style:` — Changes that do not affect the meaning of the code (formatting, etc.)
- `refactor:` — A code change that neither fixes a bug nor adds a feature
- `test:` — Adding or correcting tests
- `chore:` — Changes to the build process or auxiliary tools

### License

By contributing to this project, you agree that your contributions will be licensed under the [MIT License](LICENSE).

---

**Questions?** Feel free to open a discussion issue or reach out via GitHub Issues.
