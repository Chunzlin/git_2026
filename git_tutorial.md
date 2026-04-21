## 1. Git 基础配置

安装 Git 后，首先配置你的用户名和邮箱，每次提交都会记录这些信息。

```bash
git config --global user.name "你的名字"
git config --global user.email "你的邮箱"
```

其他常用配置：

- 设置默认分支名为 `main`（与 GitHub 保持一致）：

  ```bash
  git config --global init.defaultBranch main
  ```
- 查看所有配置：`git config --list`

## 2. 创建本地仓库

在项目根目录执行：

```bash
git init
```

这会创建一个隐藏的 `.git` 文件夹，用于版本控制。初始化后，默认分支名取决于 Git 配置（可能是 `master` 或 `main`）。建议将分支重命名为 `main`：

```bash
git branch -m main   # 将当前分支重命名为 main
```

## 3. 忽略文件：.gitignore

在项目根目录创建 `.gitignore` 文件，写入不需要 Git 追踪的文件或文件夹（例如依赖包、编译产物、敏感信息等）。常见规则：

```
# 忽略特定文件夹
node_modules/
__pycache__/
.venv/

# 忽略特定文件
config.json
*.log

# 忽略所有 .tmp 结尾的文件
*.tmp
```

**注意**：如果文件已被 Git 跟踪，需要先将其从缓存移除：

```bash
git rm -r --cached <文件或文件夹>
```

## 4. 基本工作流：add、commit、status、log

### 查看状态

```bash
git status
```

显示工作区和暂存区的状态（哪些文件被修改、新增、删除）。

### 添加文件到暂存区

- 添加所有更改（包括新增、修改、删除）：

  ```bash
  git add .
  ```
- 添加单个文件：

  ```bash
  git add 文件名
  ```

### 提交更改

```bash
git commit -m "提交说明"
```

提交时尽量写清晰的消息，说明本次更改的目的。

### 查看提交历史

```bash
git log            # 完整历史
git log --oneline  # 简洁模式（一行显示一个提交）
git log --graph    # 图形化显示分支合并
```

## 5. 远程仓库（以 GitHub 为例）

### 关联远程仓库

先在 GitHub 上创建一个空仓库（不要勾选“初始化 README”）。复制仓库 URL（HTTPS 或 SSH），然后：

```bash
git remote add origin https://github.com/你的用户名/仓库名.git
```

- `origin` 是远程仓库的**默认名称**，你可以随意取名，但通常都用 `origin`。
- 如果使用 SSH 方式，URL 类似：`git@github.com:用户名/仓库名.git`，SSH 需要提前配置密钥。

### 查看远程仓库

```bash
git remote -v
```

显示已关联的远程仓库地址（fetch 和 push 地址）。

### 修改或删除远程仓库

- 修改 URL：`git remote set-url origin 新URL`
- 删除关联：`git remote remove origin`

## 6. 推送代码到远程：git push

### 基本语法

```bash
git push <远程仓库名> <本地分支名>
```

例如：

```bash
git push origin main
```

将本地的 `main` 分支推送到名为 `origin` 的远程仓库。

### 首次推送：-u 参数

```bash
git push -u origin main
```

-  **`-u`**（`--set-upstream` 的缩写）：将本地分支与远程分支建立**追踪关系**。执行后，Git 会记住当前本地分支对应的远程分支，后续只需 `git push` 或 `git pull` 即可，无需再指定远程和分支名。
- 首次推送时通常需要 `-u`，后续推送可以直接用 `git push`。

### 其他常用参数

| 参数 | 说明                                             |
| ------ | -------------------------------------------------- |
| `-f` 或 `--force` | 强制推送，覆盖远程分支（**慎用**，可能导致他人丢失提交） |
| `--all`     | 推送所有本地分支                                 |
| `--tags`     | 推送标签（不会自动推送标签，需显式添加）         |
| `-d` 或 `--delete` | 删除远程分支，如 `git push origin --delete 分支名`                                |

### 常见错误及解决

- **推送被拒绝（rejected）** ：远程仓库已有本地没有的提交（如初始化时添加的 README）。需要先拉取合并：

  ```bash
  git pull origin main --allow-unrelated-histories
  ```

  然后解决冲突，再推送。
- **无法访问（fatal: unable to access）** ：通常是网络或代理问题，可尝试更换 SSH 协议或调整代理设置。

## 7. 拉取远程更新：git pull 和 git fetch

### git fetch

仅从远程下载最新数据，**不自动合并**到工作区。需要手动合并：

```bash
git fetch origin        # 下载远程所有更新
git merge origin/main   # 将远程 main 分支合并到当前分支
```

### git pull

相当于 `git fetch` + `git merge`，一步完成下载并合并：

```bash
git pull origin main
```

如果本地分支已与远程建立追踪关系，可直接 `git pull`。

### 处理合并冲突

当拉取时出现冲突，Git 会标记冲突文件，需要手动编辑解决，然后：

```bash
git add 冲突文件
git commit -m "解决冲突"
```

## 8. 分支管理

### 查看分支

```bash
git branch          # 本地分支列表
git branch -r       # 远程分支列表
git branch -a       # 所有分支（本地+远程）
```

### 创建分支

```bash
git branch 新分支名          # 基于当前提交创建新分支
git checkout -b 新分支名     # 创建并切换到新分支
# 或使用新版命令
git switch -c 新分支名
```

### 切换分支

```bash
git checkout 分支名
# 或
git switch 分支名
```

### 合并分支

将指定分支合并到当前分支（如将 feature 分支合并到 main）：

```bash
git checkout main
git merge feature
```

### 删除分支

- 删除本地分支：`git branch -d 分支名`（如果分支未合并，需用 `-D` 强制删除）
- 删除远程分支：`git push origin --delete 分支名`

## 9. 解决冲突

当合并或拉取时出现冲突，Git 会在文件中插入冲突标记：

```
<<<<<<< HEAD
当前分支的内容
=======
被合并分支的内容
>>>>>>> feature
```

手动编辑文件，保留需要的内容，删除标记，然后：

```bash
git add 文件名
git commit -m "解决冲突"
```

## 10. 撤销更改

### 撤销工作区的修改

```bash
git restore 文件名          # 丢弃工作区的更改（恢复到暂存区或上次提交的状态）
```

### 撤销暂存区的文件

```bash
git restore --staged 文件名   # 将文件从暂存区移出，但保留工作区的修改
```

### 撤销提交

- **git reset**：移动 HEAD 指针，**会丢失之后的提交**（慎用）。

  ```bash
  git reset --soft HEAD~1    # 撤销上次提交，但保留更改在暂存区
  git reset --hard HEAD~1    # 彻底撤销上次提交，丢弃所有更改（危险）
  ```
- **git revert**：创建一个新的提交来撤销指定提交，**更安全**（适合公共分支）。

  ```bash
  git revert HEAD            # 撤销最近一次提交，生成一个新提交
  ```

## 11. 常用命令速查表

| 命令 | 说明                 |
| ------ | ---------------------- |
| `git init`     | 初始化仓库           |
| `git add .`     | 添加所有更改到暂存区 |
| `git commit -m "msg"`     | 提交暂存区更改       |
| `git status`     | 查看状态             |
| `git log --oneline`     | 简洁查看历史         |
| `git remote add origin <url>`     | 关联远程仓库         |
| `git push -u origin main`     | 首次推送并建立追踪   |
| `git push`     | 推送（已追踪分支）   |
| `git pull`     | 拉取并合并           |
| `git branch`     | 查看分支             |
| `git checkout -b new`     | 创建并切换分支       |
| `git merge branch`     | 合并分支             |
| `git restore <file>`     | 撤销工作区修改       |
| `git remote -v`     | 查看远程地址         |

---
