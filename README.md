[TOC]

# Git

https://geeeeeeeeek.github.io/git-recipes/

Git 是目前世界上被最广泛使用的现代软件版本管理系统。Git 使用分散式架构，是分散式版本管理 DVCS（Distributed Version Control System）的代表。相较于例如 CVS 或者 Subversion 等集中式版本管理软件，Git 并不是将代码的所有修改历史保存在中心服务器中。

在 Git 中所有的操作包括提交修改，创建分支，融合分支，以及求取差分都经过了性能优化。Git 在决定代码修改历史以及保存形式的时候不会被文件名的变化所愚弄，Git 关注的是文件的内容本身。在实际操作中，代码文件经历频繁的再命名，分解和合并。Git 使用一种混合了差分编码（delta encoding，仅保存代码修改的差分），压缩，直接保存，以及版本元数据（version metadata objects）的管理方式。

安全性：所有的文件内容，文件相互关系，以及文件目录结构，版本，标签以及修改，都经过加密哈希校验算法（SHA1）的保护。
柔软性：支持各种非线性的开发工作流程，对或大或小的软件项目都可以良好支持，以及兼容各种操作系统和协议。

# 概览

## 安装

Mac 用户：Xcode Command Line Tools 自带 Git (`xcode-select --install`)
Linux 用户：`sudo apt-get install git` 
Windows 用户：下载 [Git SCM](https://github.com/geeeeeeeeek/git-recipes/wiki/git-for-windows.github.io)

## 快速指南

复制仓库：
执行如下命令以创建一个本地仓库的克隆版本：`git clone /path/to/repository`
远端服务器上的仓库： `git clone username@host:/path/to/repository`(通过 SSH)
                 `git clone https:/path/to/repository.git`(通过 https)

创建新仓：
新建文件夹，打开后`git init`创建。
通过`git status`查看状态。

工作流：
本地仓库由 git 维护的三棵“树”组成。第一个是`工作目录`，它持有实际文件；第二个是`缓存区(Index)`，临时保存改动；最后是`HEAD`，指向最近一次提交后的结果。(HEAD 一般是指向最新一次 commit 的引用)

添加与提交：
添加改动到缓存区：`git add < filename >`
实际提交改动：`git commit -m "代码提交信息"`(提交到了 HEAD，但是还没到远端仓库)

推送改动：
推送到master分支：`git push origin master`
还没克隆现有仓库，并欲将仓库连接到某远程服务器：`git remote add origin <server>`

# 创建仓库

1. `git init` 命令创建一个新的 Git 仓库。将**已存在但还没有版本控制的项目**转换成一个 Git 仓库，或者创建一个**空的新仓库**。
此命令会在你项目的根目录下创建一个新的 .git 目录，其中包含了你项目必需的所有元数据。
2. `git init <directory>`在指定目录创建一个空的 Git 仓库。运行这个命令会创建一个名为 directory，只包含 .git 子目录的空目录
3. `git clone <repo> ` 命令拷贝整个 Git 仓库。clone 自动创建了一个名为 origin 的远程连接，指向原有仓库
4. `git config` 命令允许你在命令行中配置你的 Git 安装。所有配置项都储存在纯文本文件中
`git config user.name <name>`定义当前仓库所有提交使用的作者姓名
`git config --global user.name <name>`使用 --global 标记设置当前用户的配置项
`git config --global user.email <email>`定义当前用户所有提交使用的作者邮箱。
`git config --global alias.<alias-name> <git-command>`为Git命令创建一个快捷方式
`git config --system core.editor <editor>`定义当前机器所有用户使用命令时用到的文本编辑器

举例：
```python
seucar@seucar:~$ git config --global user.name "hillvanwalker"
seucar@seucar:~$ git config --global user.email 365706975@qq.com
seucar@seucar:~$ git config --global core.editor gedit
# 添加一些快捷方式(别名)
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.up rebase
git config --global alias.ci commit
```

# 保存更改

## 缓存
1. `git add` 命令将工作目录中的变化添加到缓存区。实际上并不会改变你的仓库。直到你运行 git commit ，更改都没有真正被记录
2. `git add` 和 `git commit` 这两个命令组成了最基本的 Git 工作流。使用 `git add` 缓存更改，使用 `git commit` 提交到项目历史，`git reset`用于撤销提交或被缓存的快照
3. `git push` 用于将提交的更改发送到远端仓库
```python
git add <文件>  # 缓存 <文件> 中的更改，准备下次提交
git add <目录>  # 缓存 <目录> 下的更改，准备下次提交
git add -p     # 开始交互式的缓存
==================================================
# 初次使用，为当前目录创建首个提交
git add .
git commit
# 新的文件可以通过路径传递给 git add 来添加到缓存区
git add hello.py
git commit
```
## 提交

1. `git commit`命令将缓存的快照提交到项目历史。它会运行文本编辑器，等待你输入提交信息。当你输入信息之后，保存文件，关闭编辑器，创建实际的提交。
2. `git commit -m "<message>"`提交已经缓存的快照。将 <message> 作为提交信息
3. `git commit -a`提交一份包含工作目录所有更改的快照(只包含跟踪过的文件的更改，即通过 git add 添加过的)
实例：
```python
# 编辑了 hello.py 文件的一些内容，用 git add 缓存文件，然后提交缓存的快照
git add hello.py
git commit
# 此时会打开一个文件编辑器，询问提交信息，同时列出将被提交的文件
# 提交信息的格式约定
# 在第一行用 50 个以内的字符总结这个提交，留一空行，然后详细阐述具体的更改
Change the message displayed by hello.py

- Update the sayHello() function to output the user's name
- Change the sayGoodbye() function to a friendlier message
```

## 状态
`git status` 命令显示工作目录和缓存区的状态。可以看到哪些更改被缓存了，哪些还没有，以及哪些还未被 Git 追踪。
```python
# On branch master
# Changes to be committed:
# (use "git reset HEAD <file>..." to unstage)
#
#modified: hello.py
#
# Changes not staged for commit:
# (use "git add <file>..." to update what will be committed)
# (use "git checkout -- <file>..." to discard changes in working directory)
#
#modified: main.py
#
# Untracked files:
# (use "git add <file>..." to include in what will be committed)
#
#hello.pyc
```
未追踪的文件通常有两类。它们要么是项目新增但还未提交的文件，要么是像 .pyc、.obj、.exe 等编译后的二进制文件。将下面这行加入项目根目录的.gitignore文件可以避免编译后的Python模块出现在git status中：`*.pyc`

`git log` 命令显示已提交的快照。
1. `git log -n <limit>` 限制提交的数量。比如` git log -n 3` 只会显示 3 个提交。
2. `git log --oneline`将每个提交压缩到一行
3. `git log --stat` 包含哪些文件被更改了，以及每个文件相对的增删行数
4. `git log -p`显示代表每个提交的一堆信息。
5. `git log --author="<pattern>"` 搜索特定作者的提交
6. `git log --author="John Smith" -p hello.py`可以将很多选项用在同一个命令中。这个命令会显示 John Smith 作者对 hello.py 文件所做的所有更改的差异比较

## 历史
`git checkout` 这个命令有三个不同的作用：检出文件、检出提交和检出分支。检出提交会使工作目录和这个提交完全匹配。检出文件使你能够查看某个特定文件的旧版本。
1. `git checkout master`回到 master 分支，回到项目「当前」状态。
2. `git checkout <commit> <file>`查看文件之前的版本。它将工作目录中的 <file> 文件变成 <commit> 中那个文件的拷贝，并将它加入缓存区。
3. `git checkout <commit>`更新工作目录中的所有文件，使得和某个特定提交中的文件一致。
4.  <commit>可以将提交的哈希字串，或是标签作为 <commit> 参数
实例：
```python
# 查看之前的版本
git log --oneline
=============================
b7119f2 继续做些丧心病狂的事
872fa7e 做些丧心病狂的事
a1e8fb5 对 hello.py 做了一些修改
435b61d 创建 hello.py
9773e52 初始导入
# 查看「对 hello.py 做了一些修改」这个提交
git checkout a1e8fb5
# 此时工作目录和 a1e8fb5 提交所处的状态完全一致。
# 可以查看文件，编译项目，运行测试，甚至编辑文件。
# 所做的一切 都不会 被保存到仓库中。
===========================================
# 回到你项目的「当前」状态：
git checkout master
# 可以使用 git revert 或 git reset 来回滚任何不想要的更改
```
================================================================

`git revert`命令用来撤销一个已经提交的快照。它是通过搞清楚如何撤销这个提交引入的更改，然后在最后加上一个撤销了更改的 新 提交，而不是从项目历史中移除这个提交
1. `git revert <commit>`生成一个撤消了 <commit> 引入的修改的新提交，然后应用到当前分支
2. `git revert` 回滚了「单独一个提交」，它没有移除后面的提交

================================================================

`git reset `来重设更改(提交不再被任何引用或引用日志所引用)，这个撤销是永远的。它应该只被用于 本地 修改——你永远不应该重设和其他开发者共享的快照。

1. `git reset <file>`从缓存区移除特定文件，但不改变工作目录
2. `git reset`重设缓冲区，匹配最近的一次提交，但工作目录不变
3. `git reset --hard`重设缓冲区和工作目录，匹配最近的一次提交。--hard 标记表示要重写所有工作目录中的更改
4. `git reset <commit>`将当前分支的末端移到 <commit>，将缓存区重设到这个提交，但不改变工作目录
5. `git reset --hard <commit>`将当前分支的末端移到 <commit>，将缓存区和工作目录都重设到这个提交。
```python
# 编辑了hello.py和main.py
# 缓存了目录下所有文件
git add .
# 意识到hello.py和main.py中的修改
# 应该在不同的快照中提交
# 取消main.py缓存
git reset main.py
# 只提交hello.py
git commit -m "Make some changes to hello.py"
# 在另一份快照中提交main.py
git add main.py
git commit -m "Edit main.py"
```

================================================================

`git clean` 命令将未跟踪的文件从你的工作目录中移除。`git clean` 命令经常和 `git reset --hard `一起使用。reset 只影响被跟踪的文件，所以还需要一个单独的命令来清理未被跟踪的文件。
1. `git clean -n`执行一次git clean的『演习』。
2. `git clean -f`移除当前目录下未被跟踪的文件。
3. `git clean -df`移除未跟踪的文件，以及目录。
```python
# 编辑了一些文件
# 新增了一些文件
# 『糟糕』
# 将跟踪的文件回滚回去
git reset --hard
# 移除未跟踪的文件
git clean -df
```

================================================================

`git commit --amend` 命令是修复最新提交的便捷方式。它允许你将缓存的修改和之前的提交合并到一起，而不是提交一个全新的快照。**合并缓存的修改和上一次的提交，用新的快照替换上一个提交。**
```python
# 编辑 hello.py 和 main.py
git add hello.py
git commit
# 意识到你忘记添加 main.py 的更改
git add main.py
git commit --amend --no-edit         # 加入 --no-edit 标记会修复提交但不修改提交信息
```
# 远程管理
## 同步
`git remote`命令允许你创建、查看和删除和其它仓库之间的连接。
1. `git remote -v`列出你和其他仓库之间的远程连接，同时显示每个连接的 URL
2. `git remote add <name> <url>`创建一个新的远程仓库连接。将 <name> 作为 <url> 别名
3. `git remote rm <name>`移除名为的远程仓库的连接。
4. `git remote rename <old-name> <new-name>`将远程连接从 <old-name> 重命名为 <new-name>
5. 用 `git clone` 克隆仓库时，它自动创建了一个名为` origin `的远程连接，指向被克隆的仓库
6. Git 支持多种方式来引用一个远程仓库：HTTP 和 SSH协议

================================================================

`git fetch `命令将提交从远程仓库导入到你的本地仓库。拉取下来的提交储存为远程分支，而不是我们一直使用的普通的本地分支
1. `git fetch <remote>`拉取仓库中所有的分支。同时会从另一个仓库中下载所有需要的提交和文件
2. `git fetch <remote> <branch>`只拉取指定的分支。
3. fetch 下来的内容表示为一个远程分支，因此不会影响你的本地开发
4. 要查看远程分支，只需要向 git branch 命令传入 -r 参数。（会处于分离 HEAD 状态）
5. 远程分支拥有 remote 的前缀，所以不会将它们和本地分支混起来。
6. 同步你的本地仓库和远程仓库事实上是一个分两步的操作：先 `fetch`，然后 `merge`。
7. `git pull` 命令是这个过程的快捷方式。

================================================================

`git pull <remote>`拉取当前分支对应的远程副本中的更改，并立即并入本地副本。
git pull --rebase <remote>使用 git rebase 合并远程分支和本地分支

================================================================

`git push`是你将本地仓库中的提交转移到远程仓库中时要做的事。
1. fetch 将提交导入到本地分支，而 push 将提交导出到远程分支。
2. `git push <remote> <branch>`将指定的分支推送到 <remote> 上，包括所有需要的提交和提交对象。
3. `git push <remote> --force`和上一个命令相同，但即使会导致非快速向前合并也强制推送
4. `git push <remote> --all`将所有本地分支推送到指定的远程仓库。
5. 推送一个分支或是使用 --all 选项时，标签不会被自动推送上去
6. `git push <remote> --tags`--tags 将你所有的本地标签推送到远程仓库中去。

## Pull Request 
Pull Request 是一种机制，让开发者告诉项目成员一个功能已经完成。一旦 feature 分支开发完毕，开发者使用 GitHub 账号提交一个 Pull Request。它告诉所有参与者，他们需要审查代码，并将代码并入 master 分支

1. 当你提交一个 Pull Request 的时候，你做的事情是 请求（request） 另一个开发者（比如项目维护者）来 拉取（pull） 你仓库中的一个分支到他们的仓库。
2. 需要提供 4 个信息来完成一个 Pull Request：源仓库、源分支、目标仓库、目标分支。

## 分支
`git branch`分支代表了一条独立的开发流水线。看作请求全新「工作目录、缓存区、项目历史」的一种方式。
1. `git branch`列出仓库中所有分支。
2. `git branch <branch>`创建一个名为 <branch> 的分支。**不会 自动切换**到那个分支去。
3. `git branch -d <branch>`删除指定分支。会阻止你删除包含未合并更改的分支
4. `git branch -D <branch>`强制删除指定分支，即使包含未合并更改
5. `git branch -m <branch>`将当前分支命名为 <branch>。
6. 分支只是指向提交的 **指针 **。当你创建一个分支时，Git 只需要创建一个新的指针——仓库不会受到任何影响。
7. 创建 一个新的分支，要开始在上面添加提交，你需要用 `git checkout` 来选中这个分支，然后使用标准的 `git add` 和 `git commit `命令。
8. `git checkout `命令允许你切换用 `git branch `创建的分支。
9. `git checkout <existing-branch>`创建并查看 <new-branch>
10. -b 选项是一个方便的标记，告诉Git在运行 `git checkout <new-branch> `之前运行` git branch <new-branch>`。
11. `git checkout -b <new-branch> <existing-branch>`将 <existing-branch> 作为新分支的基，而不是当前分支
12. 创建一个新功能时，用 `git branch `创建分支，然后用 `git checkout` 查看。
13. 可以在一个仓库中用 `git checkout` 切换分支，同时开发几个功能。

================================================================

**分离的 HEAD**
`HEAD `是 Git 指向当前快照的引用。
`git checkout `命令内部只是更新 `HEAD`，指向特定分支或提交。
当它指向分支时，Git 不会报错，但当你 check out 提交时，它会进入`「分离 HEAD」`状态。

如果在分离 HEAD 状态开始开发新功能，没有分支可以让你回到之前的状态。

实例：
```python
# 开发新功能，创建一个专门的分支，切换过去：
git branch new-feature
git checkout new-feature
# 和以往一样提交新的快照
# 编辑文件
git add <file>
git commit -m "Started work on a new feature"
# 周而复始…
# 这些操作都被记录在 new-feature 上，和 master 完全独立，不用关心其它分支的修改
# 要回到「主」代码库时，只要 check out 到 master 分支即可
git checkout master
# 在这里，你可以选择并入完成的新功能，或者在你项目稳定的版本上继续工作。
```

================================================================

`git merge `命令允许你将 `git branch `创建的多条分支合并成一个。
1. `git merge <branch>`将指定分支并入当前分支。
2. `git merge --no-ff <branch>`将指定分支并入当前分支，但 总是 生成一个合并提交（即使是快速向前合并）

**快速向前合并**
```python
# 开始新功能
git checkout -b new-feature master
# 编辑文件
git add <file>
git commit -m "开始新功能"
# 编辑文件
git add <file>
git commit -m "完成功能"
# 合并new-feature分支
git checkout master
git merge new-feature
git branch -d new-feature
```

**三路合并**
```python
# 开始新功能
git checkout -b new-feature master
# 编辑文件
git add <file>
git commit -m "开始新功能"
# 编辑文件
git add <file>
git commit -m "完成功能"
# 在master分支上开发
git checkout master
# 编辑文件
git add <file>
git commit -m "在master上添加了一些极其稳定的功能"
# 合并new-feature分支
git merge new-feature
git branch -d new-feature
```

# 工作流
https://github.com/geeeeeeeeek/git-recipes/blob/master/sources/3.5-%E5%B8%B8%E8%A7%81%E5%B7%A5%E4%BD%9C%E6%B5%81%E6%AF%94%E8%BE%83.md
## 中心化

## 功能分支

## Gitflow

## Fork


















