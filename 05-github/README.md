# 05 — GitHub 协作

> 在 GitHub 上建仓库、推代码，让两个 AI 能协同编辑同一份文档。

## 安装 GitHub CLI

```bash
brew install gh
```

## 登录认证

GitHub CLI 支持两种登录方式：

### 浏览器登录（推荐）
```bash
gh auth login -h github.com -w
# 会生成一次性验证码，打开 https://github.com/login/device 输入即可
```

### Token 登录（自动化用）
```bash
echo "ghp_xxxxxxxx" | gh auth login --with-token
```

> ⚠️ **经典踩坑**：gh auth login **三步走**——第一次命令敲错参数、第二次浏览器超时、第三次柚子授权了但进程死了。重试三四次才成功。如果你发现 gh 说 "You are not logged in"，先检查 ssh/https 协议是否切换正确。

## 全局 Git 配置

```bash
git config --global user.name "your-username"
git config --global user.email "your-email@example.com"
```

## 创建仓库

```bash
# 公开仓库
gh repo create my-project --public --description "项目描述"

# 私有仓库
gh repo create my-project --private
```

## 配置 Git 协议

推荐用 SSH（省去每次输密码）：

```bash
gh config set git_protocol ssh
gh auth setup-git
```

如果用 HTTPS，需要用 token 代替密码：

```bash
git remote set-url origin https://x-access-token:$(gh auth token)@github.com/username/repo.git
```

## 推代码

```bash
cd my-project
git init
git add .
git commit -m "初始提交"
git branch -M main
git remote add origin https://github.com/username/my-project.git
git push -u origin main
```

## env 文件最佳实践

API 密钥放在 `~/.openclaw/service-env/` 下：

```bash
# ai.openclaw.gateway.env
export DEEPSEEK_API_KEY='sk-xxx'    # DeepSeek API 密钥
export ANTHROPIC_API_KEY='sk-xxx'    # Claude API 密钥
```

> ⚠️ **经典踩坑**：env 文件里 `export DEEPSEEK_API_KEY='"sk-xxx"'` 多了一层引号，导致 curl 实际读到的是带引号的 key，API 验证全部失败。**用单引号包住值，不要在值内部再加双引号。**

验证 key 是否生效：

```bash
source ~/.openclaw/service-env/ai.openclaw.gateway.env
curl -s "https://api.deepseek.com/user/balance" -H "Authorization: Bearer $DEEPSEEK_API_KEY"
```

## 跨 AI 协作流程

两个 AI 同时推一个仓库时：

1. **推之前先 pull**：`git pull origin main --rebase`
2. **各自负责不同目录**：避免同一文件冲突
3. **commit 信息写清楚**：标明哪一章、改了什么
4. **有问题开 Issue**：比桥接通信更适合深度讨论

## 速查表

| 操作 | 命令 |
|------|------|
| 登录 | `gh auth login -h github.com -w` |
| 创建仓库 | `gh repo create name --public` |
| 查看仓库 | `gh repo view owner/name` |
| 搜索项目 | `gh search repos "关键词"` |
| 推代码 | `git push -u origin main` |
| 拉更新 | `git pull origin main --rebase` |
| 切协议 | `gh config set git_protocol ssh` |
