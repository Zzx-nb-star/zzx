---
name: "git-pusher"
description: "Assists with git init, commit, and push to GitHub via SSH. Invoke when user asks to commit and push code to a GitHub repository or upload project files."
---

# Git 自动推送助手

本 Skill 用于在 Windows + PowerShell 环境下，一键完成项目的 git 初始化、提交和推送到 GitHub 的完整流程。

## 触发条件

当用户说出以下意图时自动触发：
- "提交到 GitHub"、"push 到远程仓库"
- "把项目 commit 并 push"
- "上传代码到 GitHub"
- 给出 GitHub 仓库地址并要求推送

## 操作流程

### 1. 前置检查
- 确认 git 命令可用：`git --version`
- 确认当前目录是否已是 git 仓库：`git status`
- 确认 SSH 公钥已加载（用户通常会提前说明）

### 2. 身份配置（仓库级，避免沙箱权限问题）
优先使用仓库级配置而非 `--global`：
```powershell
git config user.name "<用户名>"
git config user.email "<邮箱>"
```
若用户未指定邮箱，使用 GitHub noreply 格式：`<username>@users.noreply.github.com`

### 3. 远端配置
- 优先使用 SSH 地址（已配置公钥时）：`git@github.com:<owner>/<repo>.git`
- 将用户提供的 HTTPS 地址转换为 SSH 格式
- 避免同时存在多个 origin

### 4. 提交流程（PowerShell 兼容）
- **禁止**使用 bash heredoc 语法（`cat <<'EOF'`）
- 提交信息使用单行 `-m` 或多个 `-m` 参数
- 先 `git add .` 暂存所有变更

### 5. 推送流程
- 首次推送使用：`git push -u origin main`
- 先检测默认分支名：`git symbolic-ref refs/remotes/origin/HEAD` 或 push 报错后读取建议
- 推送后输出远端仓库地址和最新 commit hash，方便用户核验

## 注意事项

- 推送前提醒用户确认仓库名和远端地址（不可逆操作）
- 若远端仓库已有内容且本地非空，提示可能需要 rebase 或 force push
- Windows PowerShell 5 与 bash 语法不通用，所有命令使用 PS 原生命令
