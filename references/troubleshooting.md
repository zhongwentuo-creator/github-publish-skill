# 故障排除手册

## 目录

- [问题 1: Git 推送超时（无错误信息）](#问题-1-git-推送超时无错误信息)
- [问题 2: Authentication failed（认证失败）](#问题-2-authentication-failed认证失败)
- [问题 3: GitHub 完全不可达（API 和 Git 协议均超时）](#问题-3-github-完全不可达api-和-git-协议均超时)
- [问题 4: SSL 证书错误（企业代理/自建证书）](#问题-4-ssl-证书错误企业代理自建证书)
- [问题 5: non-fast-forward 拒绝](#问题-5-non-fast-forward-拒绝)
- [问题 6: Permission denied（权限不足）](#问题-6-permission-denied权限不足)
- [问题 7: macOS Keychain 认证弹窗被阻塞](#问题-7-macos-keychain-认证弹窗被阻塞)
- [问题 8: GitHub CLI 未登录](#问题-8-github-cli-未登录)

---

> 💡 **以下问题面向 Skill 开发者**：在创建和维护 GitHub Actions workflow 及 Skill 元数据时可能遇到的问题。

- [问题 9: GitHub Actions Workflow 验证失败（YAML 引号不匹配）](#问题-9-github-actions-workflow-验证失败yaml-引号不匹配)
- [问题 10: 隐藏 Unicode 字符导致 YAML 解析失败](#问题-10-隐藏-unicode-字符导致-yaml-解析失败)
- [问题 11: SKILL.md 和 skill.json 字段不一致](#问题-11-skillmd-和-skilljson-字段不一致)
- [问题 12: SKILL.md frontmatter 缺少必填字段](#问题-12-skillmd-frontmatter-缺少必填字段)

---

## 问题 1: Git 推送超时（无错误信息）

**症状**：`git push` 命令卡住 15–60 秒后超时，无明确错误信息。

**根因**：macOS 系统配置了 HTTP 代理（如 Clash / V2Ray），但 Git 的 libcurl 未自动使用系统代理。

**解决**：

```bash
# 查看系统代理
scutil --proxy

# 配置 Git 使用代理（替换为实际地址和端口）
git config --local http.proxy http://127.0.0.1:7897
git config --local https.proxy http://127.0.0.1:7897

# 验证
git push origin main
```

**永久解决**：将代理配置写入全局 git 配置

```bash
git config --global http.proxy http://127.0.0.1:7897
git config --global https.proxy http://127.0.0.1:7897
```

---

## 问题 2: Authentication failed（认证失败）

**症状**：`git push` 返回 `fatal: Authentication failed` 或 `403` 错误。

**根因**：

- 凭据过期或无效（PAT 已撤销、密码已更改）
- GitHub 已停止支持密码认证，必须使用 PAT 或 SSH
- 使用了错误的认证方式（HTTPS 但无 PAT）

**解决**：

```bash
# 检查当前认证方式
git remote -v

# 方案 A：使用 SSH
git remote set-url origin git@github.com:<owner>/<repo>.git
ssh -T git@github.com

# 方案 B：使用 HTTPS + PAT
git remote set-url origin https://<PAT>@github.com/<owner>/<repo>.git
# 或配置 git credential helper
git config --global credential.helper osxkeychain
# 然后手动推送一次，输入用户名和 PAT

# 方案 C：GitHub CLI
git remote set-url origin https://github.com/<owner>/<repo>.git
gh auth login
```

---

## 问题 3: GitHub 完全不可达（API 和 Git 协议均超时）

**症状**：`git push`、`gh api`、`curl https://api.github.com` 均超时，ping github.com 无响应。

**根因**：

- 网络未连接或 DNS 解析失败
- 所在网络环境（企业/校园/地区）对 GitHub 进行封锁或限速
- 未配置代理或 VPN，或代理/VPN 本身不可用

**解决**：

```bash
# 1. 检查基础网络连通性
ping github.com
curl -I https://github.com
nslookup github.com

# 2. 检查代理/VPN 是否运行
curl -x http://127.0.0.1:7897 -I https://github.com

# 3. 配置 Git 使用代理
git config --global http.proxy http://127.0.0.1:7897
git config --global https.proxy http://127.0.0.1:7897

# 4. 若代理本身不可用，检查代理软件状态或更换节点
# 5. 企业/校园网络：联系网管确认是否限制 GitHub 访问，或申请白名单
```

---

## 问题 4: SSL 证书错误（企业代理/自建证书）

**症状**：`git push` 或 `curl` 返回 `SSL certificate problem: unable to get local issuer certificate` 或 `self-signed certificate in certificate chain`。

**根因**：

- 企业网络使用自签证书或 MITM 代理拦截 HTTPS 流量
- Git/curl 不信任企业 CA 证书

**解决方式 1（临时，仅限开发环境）**：

```bash
git config --global http.sslVerify false
git config --global https.sslVerify false
```

> ⚠️ 会降低安全性，仅建议在受控内网临时使用。

**解决方式 2（推荐）**：导入企业 CA 证书

```bash
# macOS：将企业 CA 添加到系统钥匙串
security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain /path/to/enterprise-ca.crt

# 验证
git config --global http.sslVerify true
git push origin main
```

**解决方式 3**：使用 SSH 替代 HTTPS，绕过 SSL 证书检查

```bash
git remote set-url origin git@github.com:<owner>/<repo>.git
ssh -T git@github.com
```

---

## 问题 5: non-fast-forward 拒绝

**症状**：`git push` 返回 `rejected: non-fast-forward`。

**根因**：远程仓库有本地没有的提交，Git 拒绝覆盖。

**解决**：

```bash
# 先拉取远程最新提交
git pull origin main
# 如有冲突，手动解决后重新提交
git add -A && git commit -m "merge: resolve conflicts"

# 然后重新推送
git push origin main
```

**注意**：如果确定远程提交不需要（如新建的仓库），可以强制推送（不推荐用于协作仓库）：

```bash
git push origin main --force
```

---

## 问题 6: Permission denied（权限不足）

**症状**：`git push` 返回 `Permission denied` 或 `403 Forbidden`。

**根因**：

- GitHub 用户没有该仓库的写入权限
- 仓库属于 Organization，用户不是成员
- 尝试推送到 Fork 的仓库，但上游未授权

**解决**：

1. 检查 GitHub 仓库的 **Settings → Manage access**，确认用户有 Write 权限
2. 如果是 Fork 仓库，先提交 Pull Request 到上游，而非直接推送
3. 检查 `git remote -v` 中的 URL 是否正确（尤其是组织仓库的 URL）
4. 如果是私有仓库，确保已登录且有访问权限

---

## 问题 7: macOS Keychain 认证弹窗被阻塞

**症状**：`security` 命令或 `git push` 超时，无错误输出，看不到钥匙串弹窗。

**根因**：macOS 尝试弹出钥匙串访问密码对话框，但后台执行无法显示 GUI。

**解决方式 1**：用户手动运行一次 `git push`，在终端中输入钥匙串密码解锁。

**解决方式 2**：使用 GitHub Personal Access Token（PAT）替代钥匙串认证。

```bash
git config --global credential.helper store
# 下次推送时输入用户名和 PAT 作为密码
```

**解决方式 3**：切换到 SSH 认证方式。

```bash
git remote set-url origin git@github.com:<owner>/<repo>.git
# 确保本地已配置 SSH 密钥
ssh -T git@github.com
```

---

## 问题 8: GitHub CLI 未登录

**症状**：`gh auth status` 显示 "You are not logged into any GitHub hosts"。

**解决**：

```bash
gh auth login
# 选择 HTTPS 或 SSH，按提示完成 OAuth 流程
```

如果 Agent 环境中无法交互式完成 OAuth，可使用 Token 方式：

```bash
gh auth login --with-token <<< "YOUR_GITHUB_TOKEN"
```

---

## 问题 9: GitHub Actions Workflow 验证失败（YAML 引号不匹配）

**症状**：Actions 报错 `unexpected EOF while looking for matching '"'` 或 `Error: Process completed with exit code 2`，定位到包含 `python3 -c "..."` 的步骤。

**根因**：YAML `run: |` 块内嵌入多行 shell 命令时，`python3 -c "` 的闭合引号 `"` 缺失或被截断。

**解决方式 1（推荐）**：改用 heredoc，彻底避免引号嵌套

```yaml
- name: Validate SKILL.md
  run: |
    python3 << 'PYEOF'
    import re, yaml
    with open('SKILL.md') as f:
        content = f.read()
    # ... 验证逻辑 ...
    print('OK')
    PYEOF
```

**解决方式 2**：使用单引号包裹 `"`

```yaml
run: |
  python3 -c '
  import sys
  print("hello")
  '
```

**预防**：

- 提交前用 `cat -v` 检查文件末尾是否完整
- 在 GitHub 网页编辑后，先查看 **Preview** 确认 YAML 高亮无异常

---

## 问题 10: 隐藏 Unicode 字符导致 YAML 解析失败

**症状**：Actions 报错 `Invalid workflow file` — `You have an error in your yaml syntax`，但肉眼看不到问题。GitHub 编辑器提示 `This file contains hidden or bidirectional Unicode text`。

**根因**：从网页、聊天消息或富文本编辑器复制代码时，带入了不可见的 Unicode 控制字符（如 U+200E LTR、U+200F RTL、零宽空格等）。

**排查**：

```bash
# 查看隐藏字符
cat -v .github/workflows/validate-skill.yml

# 或用 hexdump 定位
hexdump -C .github/workflows/validate-skill.yml | head -50
```

**解决**：

1. 在本地编辑器（VS Code / Vim）中重新创建文件
2. 确保保存为 **UTF-8 无 BOM** 纯文本
3. 通过 `git push` 推送，**避免从网页/聊天直接复制到 GitHub 编辑器**

**教训**：永远不要从网页、聊天消息或邮件中直接复制代码到生产环境的 YAML 文件。

---

## 问题 11: SKILL.md 和 skill.json 字段不一致

**症状**：Actions 一致性检查报错：

```
ERROR: SKILL.md frontmatter and skill.json are inconsistent:
  description: SKILL.md="..." vs skill.json="..."
```

**根因**：修改了 SKILL.md 的 YAML frontmatter（如 description、license），但未同步更新 skill.json。

**解决**：

1. 以 skill.json 为准（它是 hashgraph 标准的元数据文件）
2. 回写 SKILL.md frontmatter，确保以下字段完全一致：
   - `name`
   - `description`
   - `license`

**检查清单**（修改 frontmatter 时勾选）：

- [ ] SKILL.md `name` == skill.json `name`
- [ ] SKILL.md `description` == skill.json `description`
- [ ] SKILL.md `license` == skill.json `license`

---

## 问题 12: SKILL.md frontmatter 缺少必填字段

**症状**：Actions 报错 `Missing required field in SKILL.md: name`，或一致性检查显示 SKILL.md 的字段为空。

**根因**：SKILL.md 的 YAML frontmatter 中遗漏了字段，或字段拼写错误（如 `compatiblity` 少了 `i`）。

**最小完整 frontmatter 模板**：

```yaml
---
name: your-skill-name
description: One-sentence description matching skill.json
license: MIT
compatibility: |
  - Agent: Kimi Work, Claude Code
  - OS: macOS, Linux
metadata:
  version: 1.0.0
  category: devops
  tags: [github, git, automation]
---
```

**校验**：本地用 Python 快速检查

```bash
python3 -c "import yaml; d=yaml.safe_load(open('SKILL.md').read().split('---')[1]); print(list(d.keys()))"
```

```bash
gh auth login --with-token <<< "YOUR_GITHUB_TOKEN"
```
