# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.0] - 2026-07-14

### Added

- Initial release of `github-publish-skill`.
- **6-Stage Pipeline**: Prepare → Commit → Diagnose → Push → Tag → Verify.
- **Auto proxy detection** for macOS (Clash / V2Ray / Surge) via `scutil` and `networksetup`.
- **Sensitive file scanning** before push: detects `.env`, files containing `secret`, `key`, `password`, `token`, and other common patterns.
- **Placeholder detection**: warns when `YOUR_USERNAME`, `YOUR_REPO`, or similar placeholders are still present in the project.
- **Automatic git commit** with semantic messages when uncommitted changes are detected.
- **Version tag creation and push**: creates annotated tags and pushes them to the remote repository.
- **Remote verification**: queries GitHub API to confirm the push succeeded and reports the latest commit hash.
- **Multi-platform compatibility**: tested on Kimi Work, Claude Code, OpenClaw, and Cursor.
- **Comprehensive documentation**:
  - `SKILL.md` with YAML frontmatter and high-level workflow.
  - `references/troubleshooting.md` covering 6 common failure scenarios.
  - `references/proxy-guide.md` with per-platform proxy configuration.
  - `references/platform-paths.md` with quick-reference install paths for all supported agents.
  - `references/examples/` with 3 detailed usage scenarios.
- **Community files**: `LICENSE` (MIT), `CONTRIBUTING.md`, `CHANGELOG.md`.
- **GitHub Action**: `.github/workflows/validate-skill.yml` for automated YAML frontmatter and `skill.json` validation.

---

## [Unreleased]

- Planned: Windows proxy auto-detection support.
- Planned: Pre-push hook integration.
- Planned: GitHub Releases creation via API.
