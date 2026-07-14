# 示例 3: 首次发布完整流程（含标签创建）

## 场景

用户完成一个全新项目，首次发布到 GitHub，需要创建仓库、推送代码、打版本标签。

---

## 用户输入

```
发布这个项目到 GitHub，版本 v1.0.0
```

---

## Agent 执行流程

> **前置条件**：已在 GitHub 创建空仓库并执行：
> ```bash
> git remote add origin https://github.com/<owner>/<repo>.git
> ```
> 若尚未创建仓库，可先执行：
> ```bash
> gh repo create <repo-name> --public --source=. --remote=origin --push
> ```

**Step 1: 检查项目准备**

```bash
$ git status
On branch main
nothing to commit, working tree clean
```

→ 无未提交更改，但检查到以下文件：

```bash
$ ls -la
total 32
drwxr-xr-x  8 user  staff  256 Jul 14 15:00 .
drwxr-xr-x  3 user  staff   96 Jul 14 14:00 ..-
-rw-r--r--  1 user  staff  10857 Jul 14 15:00 README.md
-rw-r--r--  1 user  staff  1098 Jul 14 14:00 LICENSE
-rw-r--r--  1 user  staff  680 Jul 14 14:00 .env.example
-rw-r--r--  1 user  staff  327 Jul 14 14:00 .gitignore
-rw-r--r--  1 user  staff  13579 Jul 14 15:00 main.py
-rw-r--r--  1 user  staff  113 Jul 14 14:00 requirements.txt
```

→ 项目准备检查：
- ✅ `.gitignore` 存在
- ✅ `README.md` 存在
- ✅ `LICENSE` 存在
- ✅ `.env.example` 存在（无真实 `.env`）
- ✅ 无敏感文件泄露

**Step 2: 跳过提交（无更改）**

**Step 3: 网络诊断**

→ GitHub API 可达，Git 协议连接正常，无代理问题。

**Step 4: 推送**

```bash
$ git push origin main
To https://github.com/xxx/xxx.git
 * [new branch]      main -> main
```

→ 首次推送成功。

**Step 5: 创建版本标签**

```bash
$ git tag -a v1.0.0 -m "Release v1.0.0 — Initial release"
$ git push origin v1.0.0
To https://github.com/xxx/xxx.git
 * [new tag]         v1.0.0 -> v1.0.0
```

→ 标签创建并推送成功。

**Step 6: 验证**

```bash
$ curl -s https://api.github.com/repos/xxx/xxx/tags
v1.0.0 → 4d66a71

$ curl -s https://api.github.com/repos/xxx/xxx/contents/README.md?ref=main
size: 10857 bytes
```

→ 远程仓库验证：
- 标签 `v1.0.0` 存在
- `README.md` 文件确认存在
- 共 7 个文件推送成功

---

## 最终输出

```markdown
## ✅ GitHub 发布完成

**仓库**: https://github.com/xxx/xxx
**最新提交**: `4d66a71` — Initial commit
**标签**: v1.0.0

### 项目准备检查
- [x] .gitignore 存在
- [x] README.md 存在
- [x] LICENSE 存在
- [x] .env.example 存在
- [x] 敏感文件已排除
- [x] 无占位符未替换

### 发布统计
- 文件总数: 7
- 首次推送: 是
- 版本标签: v1.0.0

### 下一步建议
- 在 GitHub 仓库页面创建 Release（关联 v1.0.0 标签）
- 设置 Topics: `python`, `automation`, `open-source`
- 启用 Issues 和 Discussions
```
