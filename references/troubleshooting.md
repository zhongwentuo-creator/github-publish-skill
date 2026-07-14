# 故障排除手册

## 1. Git 推送超时（无错误信息）

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

## 2. macOS Keychain 认证弹窗被阻塞

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

## 3. GitHub CLI 未登录

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

## 4. Authentication failed（认证失败）

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

## 5. non-fast-forward 拒绝

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

## 6. Permission denied（权限不足）

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
