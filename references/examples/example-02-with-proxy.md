# 示例 2: 代理问题自动检测与修复

## 场景

用户在使用 macOS，系统配置了 Clash 代理（127.0.0.1:7897），但 `git push` 一直超时。Agent 自动诊断并修复。

---

## 用户输入

```
发布这个项目到 GitHub
```

---

## Agent 执行流程

**Step 1: 检查项目准备**

→ 项目准备检查通过，无敏感文件，无未替换占位符。

**Step 2: 自动提交**

→ 无未提交更改，跳过此阶段。

**Step 3: 网络诊断**

```bash
$ curl -sI https://api.github.com
HTTP/2 200

$ GIT_TERMINAL_PROMPT=0 git ls-remote origin
# 命令超时，无响应...
```

→ **诊断结果**：GitHub API 可达（HTTP 200），但 Git 协议连接超时。

```bash
$ scutil --proxy
  HTTPProxy : 127.0.0.1
  HTTPSProxy : 127.0.0.1
  Port : 7897
```

→ **根因确认**：macOS 系统配置了 HTTP 代理 127.0.0.1:7897，但 Git 未配置使用代理。

**Step 4: 自动修复并推送**

```bash
$ git config --local http.proxy http://127.0.0.1:7897
$ git config --local https.proxy http://127.0.0.1:7897

$ git push origin main
To https://github.com/xxx/xxx.git
   eb2aa07..4d66a71  main -> main
```

→ 代理配置成功，推送成功。

**Step 5: 跳过标签**

**Step 6: 验证**

→ 远程提交确认通过。

---

## 最终输出

```markdown
## ✅ GitHub 发布完成

**仓库**: https://github.com/xxx/xxx
**最新提交**: `4d66a71` — chore: prepare for GitHub release

### 网络诊断
- ⚠️ 检测到系统代理: 127.0.0.1:7897
- ✅ 已自动配置 Git 使用代理

### 下一步建议
- 为避免未来推送再次超时，可运行永久配置:
  git config --global http.proxy http://127.0.0.1:7897
  git config --global https.proxy http://127.0.0.1:7897
```
