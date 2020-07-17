# Git使用

## Git配置

### 全局配置

主目录下的.gitconfig

```shell
# 设置名称信息
git config --global user.name <"Name">
# 设置邮箱信息
git config --global user.email <"Email">
# 显示颜色
git config --global color.ui true
# 设置命令别名
git config --global alias.<别名> <命令名>
```

### 局部配置

当前库的.git目录下的config，配置命令不加global就是局部配置。

#### 忽略文件

当前库的.gitignore文件

规则：

``` shell
<filename>
*.<后缀>
```



## 创建版本库

``` shell
# 创建文件夹
mkdir name
cd name
# 创建为git仓库，自动创建master分支
git init
```

## 提交文件流程

![git-repo](https://www.liaoxuefeng.com/files/attachments/919020037470528/0)

### 提交流程

``` shell
# 添加要提交的文件（可添加多个），将文件修改添加至暂存区
git add filename
# 提交文件，将暂存区内容提交到当前分支
git commit -m "注释说明信息"
# 上传至远程仓库
git push -u origin(远程仓库名) master(分支名)
```

## 恢复

### 老版

恢复分两种情况：

+ 文件修改后未添加至暂存区

此时恢复，相当于从仓库中还原最新的提交至本地(且仓库和本地文件一致，本地新建的不行)，丢弃修改。

+ 文件修改后添加至暂存区

此时恢复，相当于将添加到暂存区中的内容还原至本地

``` shell
git checkout -- <filename>
```

### 新版

+ 从暂存区恢复至工作区

```shell
git restore --worktree <filename>
```

+ 从仓库恢复至暂存区

```shell
git restore --staged <filename>
```

+ 从仓库同时恢复至暂存区和工作区

```shell
git restore --source=HEAD --staged --worktree <filename>
```

### 检查流程

``` shell
# 检查仓库当前的状态(查看是否有我们不知道的变更)
git status
# 比较工作区与暂存区的区别(如未指定文件名，则比较所有)
git diff <filename>
# 比较暂存区与仓库的区别(如未指定文件名，则比较所有)
git diff --cached <filename>
# 比较工作区与仓库的区别(如未指定文件名，则比较所有)
git diff HEAD <filename>
```

## 日志系统

``` shell
# 查看提交历史记录（第一个为当前状态,不指定参数则显示详细信息）
git log [--pretty=oneline]
# 查看命令历史记录
git reflog
```

## 回溯版本

``` shell
# 回溯上一个版本(参数HEAD^中^代表往上数第一个版本，^^为往上数第二个版本，如欲回溯的版本过前，可用HEAD~100，即往上数第一百个版本)
git reset --hard <HEAD^>
# 也可以指定回溯版本的commit号(在日志中可以查看，往上查看提交历史记录，往下查看命令历史记录)
git reset --hard <commit id>
```

## 删除文件

``` shell
# 本地删除
rm <filename>
# git删除
git rm <filename>
# 提交删除操作
git commit -m <"remove filename">
```

## 恢复文件

文件删除后想要找回，可以从上一个提交版本中恢复文件。

``` shell
git restore --source=<commit_id> --staged --worktree <filename>
```

## 远程库SSH秘钥

```shell
# 如用户主目录不存在.ssh目录，则需生成秘钥
ssh-keygen -t rsa -C <"keys">
# 进入.ssh，将公钥复制至远程仓库添加秘钥处
cd ~
cd .ssh
# id_rsa:私钥
# id_rsa.pub:公钥

```

## 远程库

### 关联远程库

**第一种方式**：关联

```shell
# 本地没有仓库
mkdir 仓库名
cd 仓库名
git init
git remote add <远程库名> git@<网络地址>:<账号名>/<仓库名>.git (注：远程库名，为本地使用，可以更改)
git pull
git branch -a
git checko <分支名>

# 本地已有仓库
cd 仓库名
git remote add <远程库名> git@<网络地址>:<账号名>/<仓库名>.git (注：远程库名，为本地使用，可以更改)
get pull
git branch -a
git checko <分支名>
```

**第二种方式**：克隆

```shell
git clone git@<网络地址>:<账号名>/<仓库名>.git
```

### 删除已有远程库

```shell
# 删除远程库
git remote rm <远程库名>
```



## 分支

``` shell
# 查看所有分支
git branch -a
# 创建分支
git branch <分支名>
# 切换已有分支
git checkout <分支名>
# 创建并切换分支
git checkout -b <分支名>
# 创建远程分支至本地(先在远程库创建分支，然后抓取至本地)，分支名需一致
git checkout -b <分支名> <远程库名>/<远程分支名>
# 合并目标分支到当前分支 启用Fast forward模式（删除分支后，丢失分支信息）
git merge <分支名>
# 合并目标分支到当前分支 禁用Fast forward模式（删除分支后，保留分支信息）
git merge --no-ff -m <"描述"> <分支名>
# 查看分支合并图
git log --graph
# 删除分支
git branch -d <分支名>
# 强制删除还未合并的分支(不建议使用)
git branch -D <分支名>
# 删除远程分支
git push <远程库名> :<分支名>
```

**新版切换分支**

``` shell
# 创建并切换分支
git switch -c <分支名>
# 切换已有分支
git switch <分支名>
```

**整理分支**变基 (尽量不使用，影响他人使用)

```shell
git rebase
```



## BUG修复流程

```shell
# 当a分支有正在进行的任务时
# 暂存当前工作区
git stash
# 切换至BUG分支b
git checkout b
# 创建并切换处理BUG分支b-01
git checkout -b b-01
# 处理完BUG后 切换至BUG分支b
git switch b
# 合并处理BUG分支，并保留分支信息
git merge --no-ff -m "描述" b-01
# 切换至正进行任务的分支
git switch a
# 查看暂存工作区
git stash list
# 回复工作区（方式一），保留stash内容
git stash apply
# 回复工作区（方式二），移除stash内容
git stash pop

# 可恢复指定stash，0为序号
git stash apply stash@{0}

```

**如当前分支a是从b分支出来的，需要将a中的BUG也一并修复**

```shell
# 在a分支中修复BUG commit id为在b-01中commit所产生commit id
git cherry-pick <commit id>
```

## 多人协作

**远程库操作**

```shell
# 查看远程库信息
git remote
# 查看详细信息(fetch:抓取权限,push:推送权限)
git remote -v
```

**推送操作**

```shell
# 推送本地分支至远程库对应分支
git push <远程库名> <本地分支名>
```

**抓取失败**

```shell
# 错误信息：no tracking information(本地分支未链接远程分支)
# 设置链接
git branch --set-upstream-to = <远程库名>/<远程分支名> <分支名>
```

**情景一：其他人对分支推送了，你推送的与其冲突**

```shell
# 首先抓取合并最新提交
git pull
# 解决冲突后，重新推送
git push <远程库名> <本地分支名>
```

## 标签管理

```shell
# 创建当前分支的标签
git tag <tagname>
# 创建特定commit id 标签
git tag <tagname> <commit id>
# 创建当前分支带描述的标签
git tag -a <tagname> -m <"text">
# 创建特定commit id带描述的标签
git tag -a <tagname> -m <"text"> <commit id>
# 删除标签
git tag -d <tagname>


# 查看标签信息
git show <tagname>


# 向远程库推送标签
git push <远程库名> <tagname>
# 向远程库推送所有标签
git push <远程库名> --tags
# 删除远程库标签
git push <远程库名> :refs/tags/<tagname>
```

## 总结

+ 从Git服务器/平台创建仓库后，关联/克隆至本地
+ 从主分支master创建一个分支develop，切换至develop
+ 如需要开发某功能时，从分支develop创建develop-功能名，切换至develop-功能名
+ 在develop-功能名，完成开发后，切换至develop，合并分支develop-功能名，视情况决定是否删除develop-功能名
+ 当a的第一个版本开发完时，在a创建Release-版本id，切换至Release-版本id
+ 在Release-版本id，完成版本号修改及其他工作，分别与develop、master合并，并在master打上tag(标签)，视情况决定是否删除Release-版本id
+ 如已发布版本出现紧急BUG，切换至master/tagname，创建hotfix-版本id，切换至hotfix-版本id
+ 在hotfix-版本id，完成BUG修复后，分别与develop、master合并，视情况决定是否删除hotfix-版本id

![img](https://nvie.com/img/git-model@2x.png)