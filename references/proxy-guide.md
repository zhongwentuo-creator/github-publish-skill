# 代理配置指南

## macOS

### 自动检测命令
```bash
scutil --proxy
```

### 常见代理工具默认端口

| 工具 | 默认 HTTP 端口 | 默认 SOCKS 端口 | 检测方式 |
|------|---------------|----------------|----------|
| Clash Verge | 7897 | 7898 | `scutil --proxy` |
| ClashX | 7890 | 7891 | `scutil --proxy` |
| V2Ray | 10809 | 10808 | 手动查看 GUI 设置 |
| Surge | 6152 | 6153 | `scutil --proxy` |
| Shadowsocks | 1080 | 1080 | 手动查看配置 |

### 配置 Git 使用系统代理
```bash
# 获取当前系统代理（示例输出解析）
# 若 scutil --proxy 显示 HTTPProxy: 127.0.0.1, HTTPPort: 7897
# 则配置：
git config --local http.proxy http://127.0.0.1:7897
git config --local https.proxy http://127.0.0.1:7897

# 验证
git config --local --get http.proxy
git config --local --get https.proxy
```

### 全局配置（所有仓库）
```bash
git config --global http.proxy http://127.0.0.1:7897
git config --global https.proxy http://127.0.0.1:7897
```

### 取消代理
```bash
git config --local --unset http.proxy
git config --local --unset https.proxy
# 或全局
git config --global --unset http.proxy
git config --global --unset https.proxy
```

---

## Linux

### 环境变量方式（当前终端）
```bash
export http_proxy=http://127.0.0.1:7897
export https_proxy=http://127.0.0.1:7897
export HTTP_PROXY=http://127.0.0.1:7897
export HTTPS_PROXY=http://127.0.0.1:7897

# 验证
env | grep -i proxy
```

### Git 配置方式（推荐）
```bash
git config --local http.proxy http://127.0.0.1:7897
git config --local https.proxy http://127.0.0.1:7897
```

### 永久配置（写入 ~/.bashrc 或 ~/.zshrc）
```bash
# 在 ~/.bashrc 或 ~/.zshrc 末尾添加
export http_proxy=http://127.0.0.1:7897
export https_proxy=http://127.0.0.1:7897
export no_proxy=localhost,127.0.0.1,.local
```

然后执行：
```bash
source ~/.bashrc  # 或 source ~/.zshrc
```

### 系统级代理（Ubuntu/Debian）
编辑 `/etc/environment`：
```
http_proxy=http://127.0.0.1:7897
https_proxy=http://127.0.0.1:7897
```

注销并重新登录后生效。

---

## Windows

### Git Bash
```bash
# 当前终端
git config --local http.proxy http://127.0.0.1:7897
git config --local https.proxy http://127.0.0.1:7897

# 或全局
git config --global http.proxy http://127.0.0.1:7897
git config --global https.proxy http://127.0.0.1:7897
```

### PowerShell
```powershell
# 当前会话
$env:http_proxy = "http://127.0.0.1:7897"
$env:https_proxy = "http://127.0.0.1:7897"

# 验证
Invoke-WebRequest -Uri "https://api.github.com" -Method Head

# 永久配置（用户环境变量）
[Environment]::SetEnvironmentVariable("http_proxy", "http://127.0.0.1:7897", "User")
[Environment]::SetEnvironmentVariable("https_proxy", "http://127.0.0.1:7897", "User")
```

### Windows 命令提示符（CMD）
```cmd
set http_proxy=http://127.0.0.1:7897
set https_proxy=http://127.0.0.1:7897

# 永久配置（系统属性 → 环境变量）
setx http_proxy http://127.0.0.1:7897
setx https_proxy http://127.0.0.1:7897
```

### 常见 Windows 代理工具端口

| 工具 | 默认 HTTP 端口 |
|------|---------------|
| Clash for Windows | 7890 |
| v2rayN | 10809 |
| SSR (ShadowsocksR) | 1080 |

---

## 快速验证

配置完成后，验证代理是否生效：
```bash
# 测试 Git 连接
git ls-remote https://github.com/github/gitignore.git HEAD

# 测试 API 连接
curl -sI --connect-timeout 5 https://api.github.com

# 若以上均成功，代理配置正确
```
