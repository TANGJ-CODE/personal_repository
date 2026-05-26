# Git 常用命令

## 基础操作

```bash
# 初始化仓库
git init

# 克隆远程仓库
git clone <url>

# 查看状态
git status

# 添加到暂存区
git add <文件>          # 单个文件
git add .              # 当前目录所有文件
git add -A             # 所有变更（含删除）

# 提交
git commit -m "描述"
git commit -am "描述"  # 跳过暂存（仅限已跟踪文件）
```

## 查看与比较

```bash
# 查看提交历史
git log
git log --oneline -10  # 简洁最近10条
git log --graph        # 图形化分支

# 查看变更
git diff              # 工作区 vs 暂存区
git diff --staged     # 暂存区 vs 上次提交
git diff HEAD         # 工作区 vs 上次提交
```

## 分支管理

```bash
# 查看分支
git branch

# 创建分支
git branch <分支名>

# 切换分支
git checkout <分支名>
git switch <分支名>

# 创建并切换
git checkout -b <分支名>
git switch -c <分支名>

# 删除分支
git branch -d <分支名>
```

## 远程仓库

```bash
# 查看远程仓库
git remote -v

# 添加远程仓库
git remote add origin <url>

# 推送
git push origin <分支名>
git push -u origin <分支名>  # 首次推送并关联

# 拉取
git pull
git pull origin <分支名>

# 获取（不合并）
git fetch
```

## 撤销操作

```bash
# 撤销工作区修改
git restore <文件>

# 撤销暂存
git restore --staged <文件>

# 撤销提交（保留修改）
git reset --soft HEAD~1

# 撤销提交（丢弃修改）
git reset --hard HEAD~1

# 回滚到指定提交
git revert <commit-id>
```

## 常用工作流

```bash
# 1. 拉取最新代码
git pull

# 2. 创建功能分支
git checkout -b feature/xxx

# 3. 修改代码
# ... 编码 ...

# 4. 提交变更
git add .
git commit -m "feat: 添加xxx功能"

# 5. 推送
git push -u origin feature/xxx
```

## 常见问题

```bash
# 解决合并冲突
git status              # 查看冲突文件
# 手动编辑冲突文件，解决后
git add <冲突文件>
git commit

# 暂存当前工作（临时切换）
git stash
git stash pop           # 恢复暂存

# 清理未跟踪文件
git clean -f            # 删除文件
git clean -fd           # 删除文件和目录
```

## 提交信息规范（Conventional Commits）

| 类型 | 说明 |
|------|------|
| `feat:` | 新功能 |
| `fix:` | 修复 bug |
| `docs:` | 文档更新 |
| `style:` | 代码格式调整 |
| `refactor:` | 重构 |
| `test:` | 测试相关 |
| `chore:` | 构建/工具链配置 |

示例：
```
feat: 添加用户登录功能
fix: 修复订单计算错误
docs: 更新 API 文档
```