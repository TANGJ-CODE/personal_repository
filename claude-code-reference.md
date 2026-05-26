# Claude Code 常用命令与调试参考

## 基础命令

### 斜杠命令

| 命令 | 描述 |
|------|------|
| `/help` | 显示帮助信息 |
| `/clear` | 清空当前对话历史 |
| `/config` | 配置设置（主题、模型等） |
| `/loop <间隔> <命令>` | 设置循环执行任务 |
| `/tasks` | 查看任务列表 |
| `/memory` | 查看内存系统状态 |
| `/skills` | 列出可用技能 |

### 技能命令

| 命令 | 描述 |
|------|------|
| `/init` | 初始化 CLAUDE.md 文件 |
| `/review` | 审查 PR |
| `/run` | 运行应用程序 |
| `/verify` | 验证代码变更 |
| `/code-review` | 代码审查 |
| `/security-review` | 安全审查 |

## 调试技巧

### 1. 查看当前状态

```bash
# 查看当前目录内容
ls -la

# 查看 git 状态
git status
git log --oneline -10

# 查看环境变量
env | grep CLAUDE
```

### 2. 权限问题

如果遇到权限被拒绝的问题：

```bash
# 检查文件权限
ls -l <文件路径>

# 修改权限（谨慎使用）
chmod +x <文件>
chmod 644 <文件>
```

### 3. 配置文件位置

```
~/.claude/settings.json          # 全局设置
~/.claude/settings.local.json    # 本地覆盖设置
~/.claude/keybindings.json       # 键盘快捷键
~/.claude/hooks/                 # 钩子脚本
.claude/settings.json            # 项目级设置
.claude/memory/                  # 项目记忆系统
.claude/scheduled_tasks.json     # 定时任务
```

### 4. 日志查看

```bash
# 查看最近日志
tail -f ~/.claude/logs/claude.log

# 搜索错误
grep -i error ~/.claude/logs/claude.log
```

## 常用交互模式

### 1. 独立对话

```
# 在新对话中开始工作
! <命令>  # 在当前会话中运行 shell 命令
```

### 2. 工作树模式

```
# 创建隔离的工作环境
worktree create <名称>
worktree remove
worktree keep
```

### 3. 计划模式

```
# 进入计划模式
/plan <任务描述>
# Claude 会先探索代码，制定计划，等待批准后再执行
```

## 代码操作最佳实践

### 1. 多步骤任务

```
# Claude 会自动创建任务列表跟踪进度
# 也可以手动创建任务
```

### 2. 安全操作

```bash
# 危险操作前 Claude 会询问确认
# 包括：删除文件、强制推送、修改 CI/CD 等
```

### 3. Git 工作流

```bash
# Claude 推荐的 Git 流程
git status   # 查看状态
git diff     # 查看变更
git log      # 查看提交历史

# 创建提交时 Claude 会：
# 1. 并行运行 git status, diff, log
# 2. 分析变更并起草提交信息
# 3. 使用 HEREDOC 格式确保正确性
```

## 高级功能

### 1. 记忆系统

```bash
# 项目记忆位置
.claude/memory/

# 记忆类型：
# - user: 用户角色、偏好
# - feedback: 工作方式指导
# - project: 项目上下文
# - reference: 外部资源链接
```

### 2. 定时任务

```bash
# 设置定时提醒/任务
/cron "0 9 * * *" "检查部署状态"
```

### 3. 并行执行

```
# Claude 会自动并行执行独立任务
# 例如：同时运行多个独立的 shell 命令
```

## 常见问题排查

### 权限提示过多

```bash
# 使用 fewer-permission-prompts 技能
/claude-code-skill fewer-permission-prompts
# 扫描常用操作并添加到允许列表
```

### 工作树问题

```bash
# 检查工作树列表
git worktree list

# 清理孤立工作树
git worktree prune
```

### 响应慢

```bash
# 检查网络连接
ping api.anthropic.com

# 检查 API 配置
cat ~/.claude/settings.json | grep -i api
```

## 提示词技巧

### 1. 明确指定

```
差: "修复这个"
好: "修复登录时的认证失败问题"
```

### 2. 提供上下文

```
差: "添加一个功能"
好: "在用户设置页面添加退出登录按钮"
```

### 3. 指定范围

```
差: "清理代码"
好: "重构 auth.py 中的认证逻辑"
```

## 快捷键 (可自定义)

```
Ctrl+C       # 中断当前操作
Ctrl+L       # 清空屏幕
Ctrl+D       # 退出
Tab          # 自动补全
```

## 资源

- 官方文档: https://github.com/anthropics/claude-code
- 问题反馈: https://github.com/anthropics/claude-code/issues