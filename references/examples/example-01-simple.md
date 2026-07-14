# 示例 1: 简单项目发布

## 场景

用户有一个 Python 项目，已初始化 git 并配置远程仓库，想要将最新更改推送到 GitHub。

---

## 用户输入

```
发布这个项目到 GitHub
```

---

## Agent 执行流程

**Step 1: 检查项目准备**

```bash
$ git status
On branch main
Your branch is ahead of 'origin/main' by 3 commits.
Changes not staged for commit:
  modified:   README.md
  modified:   config.py
  new file:   utils/helper.py
```

→ 发现 3 个未暂存文件，继续执行。

**Step 2: 自动提交**

```bash
$ git add -A
$ git commit -m "chore: prepare for GitHub release"
[main 4d66a71] chore: prepare for GitHub release
 3 files changed, 45 insertions(+), 2 deletions(-)
```

**Step 3: 网络诊断**

```bash
$ curl -sI https://api.github.com
HTTP/2 200
```

→ GitHub API 可达，无需代理配置。

**Step 4: 推送**

```bash
$ git push origin main
To https://github.com/xxx/xxx.git
   eb2aa07..4d66a71  main -> main
```

→ 推送成功。

**Step 5: 跳过标签（用户未指定版本）**

**Step 6: 验证**

```bash
$ curl -s https://api.github.com/repos/xxx/xxx/commits?sha=main&per_page=1
4d66a71 chore: prepare for GitHub release
```

→ 验证通过。

---

## 最终输出

```markdown
## ✅ GitHub 发布完成

**仓库**: https://github.com/xxx/xxx
**最新提交**: `4d66a71` — chore: prepare for GitHub release
**标签**: 无

### 项目准备检查
- [x] .gitignore 存在
- [x] README.md 存在
- [x] LICENSE 存在
- [x] 敏感文件已排除

### 警告
- 无

### 下一步建议
- 可选：创建 GitHub Release v1.0.0
- 可选：设置 Topics: `python`, `automation`
```
