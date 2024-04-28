## 初始化配置

配置用户名、邮箱

```git
git config --global user.name "John Doe"
git config --global user.email johndoe@example.com
```

查看配置

```git
git config --list
```

## 初始化项目

本地初始化，将项目纳入`git`管理

```git
git init
```

或者克隆已有的项目

```git
git clone
#克隆完默认生成origin指向远程仓，如果想不用origin这个名字，可以通过-o修改
git clone [url] -o [remote-name]
```

## 查看文件差异

查看修改后未加入缓存区的内容

```git
git diff
```

查看缓存区的内容差异

```git
git diff --cached
# 或者
git diff --staged
```

## 提交

```git
# 普通提交 填写提交信息。保存后忽略注释内容。
git commit
# 快捷提交，在后面
git commit -m "xxx"
# 跳过添加缓存区动作（git add），直接提交
git commit -a -m "xxxx"
```

## 推送

```git
git push [remote-name] [branch-name]
# -u参数可以绑定推送关系，下次推送可以不用写remote和branch
git push -u [remote-name] [branch-name]
# 举例
git push origin master
git push origin HEAD
# 上面都是默认将当前分支代码更新到origin上的同名分支上，
# 如果想改名字，操作如下：
git push origin [local-branch-name]:[remote-branch-name]
```

## 拉取

从远程仓库拉取分支信息。只会将本地没有，远程仓库有的分支数据拉取过来，但是不会自动合并，需要手动合并。同时会更新远程跟踪分支[remote-name]/[branch-name]的位置。更多信息查看远程跟踪分支一节。

```git
git fetch [remote-name]
# 假设，本地没有origin/testB分支
git fetch origin
# git fetch后只会在本地生成一个不能改变的远程跟踪分支，如：origin/testB
# 并不会在本地生成testB分支
# 假如当前分支是testA,想要testB的数据，需要自己合并
git merge origin/testB
```

本地跟踪分支与远程跟踪分支建立关联后，可以直接执行`git pull`替代`git fetch`和`git merge`操作

```git
git pull
```

## 撤销

#### commit的覆盖

此命令不会新增一条commit记录，最终只会有一条提交记录。

```git
git commit --amend
```

此命令会将缓存区的内容提交。如果`git commit`之后，有些文件忘记缓存了，可以如下操作：

```git
git commit
git add [file]
git commit --amend
```

#### git reset

`git reset`可以撤销版本记录、撤销文件和`commit`记录

查询版本记录命令：

```git
git reflog
```

撤销到指定版本，新增的修改全部丢弃，即清空缓存区和工作区

高危命令，谨慎使用

```git
git reset --hard
```

缓存区的差异保留

```git
git reset --soft 
```

从缓存区撤销到工作区，一般用于撤销git add后的文件

```git
# 可以不写mixed默认就是mixed
git reset --mixed
git reset HEAD
git reset -- [file-name]
```

#### 对文件的撤销

如果在工作区修改了文件，想将文件恢复到修改前的状态，可以使用如下高危命令

```git
git checkout -- [file-name]
```

## 远程仓库

查看远程仓库

```git
git remote -v
# 下面命令可以看到更加详细的信息
git remote -show [remote-name]
```

添加远程仓库

```git
git remote add [short-name] [url]
```

重命名远程仓库

```git
git remote rename [old-name] [new-name]
```

删除远程仓库

```git
git remote rm [remote-name]
```

## 分支操作

创建分支并切换分支

```git
git branch [branch-name]
git checkout [branch-name]
# 简写
git checkout -b [branch-name]
```

删除分支

```git
git branch -d [branch-name]
# 删除还没有合并的分支，相当于强制删除
git branch -D [branch-name]
# 删除远程分支
git push [remote-name] --delete [branch-name]
```

查看分支

```git
# 查看所有分支
git branch
# 查看所有分支及关联的最后提交对象
git branch -v
# 查看所有分支，并且显示本地跟踪分支与哪个远程分支关联
git branch -vv
```

#### 跟踪分支

###### 1、远程跟踪分支

远程跟踪分支是对远程仓库分支的本地引用。远程跟踪分支的位置不会由用户控制，由`git`自己控制。它表示上次连接远程仓时，远程分支所处的状态。如果远程仓库`origin`有`testB`分支，那本地的`origin/testB`就表示远程跟踪分支。`git fetch`操作会更新`origin/testB`的指针位置。

###### 2、本地跟踪分支

在远程仓库一节，介绍了通过`git fetch`拉取数据，并通过本地分支`testA`合并`origin`仓`testB`分支的代码。这个过程产生了远程分支`origin/testB`，但没有产生新的本地分支`testB`。

如果想在本地产生`testB`分支，并跟踪远程分支，需要将它与远程跟踪分支`origin/testB`关联起来。此时`testB`分支就叫做本地跟踪分支。

如果想要在本地生成`testB`分支，同时将本地`testB`与`origin/testB`关联起来，有如下2种方式：

第一种方式是`git checkout -b [branch-name] [remote-name]/[branch-name]`，如果前后2个`branch-name`相同，还可以简写：

<mark>疑问</mark>？：这个`[remote-name]/[branch-name]`分支需不需要已存在在本地，即是否需要先`git fetch`一下。

```git
git checkout -b testB origin/testB
# 简写
git checkout --track origin/testB
```

第二种方式是`git fetch [remote-name] [remote-branch]:[local-branch]`：

```git
git fetch orgin testB:testB
```

如果一个想为一个已有的本地分支，跟踪一个`git fetch`拉下来的远程分支，或者为一个本地分支更改它的远程分支，可以使用如下命令：

```git
# -u或者--set-upstream-to都行
git branch -u [remote-name]/[branch-name]
# 设置完毕后，可以通过@{upstream}或者@{u}快速指代远程跟踪分支
# git merge [remote-name]/[branch-name]可以写成如下：
git merge @{u}
```

###### 3、本地跟踪分支后续更新

如果服务器上远程分支更新了，本地跟踪分支想更新，有如下2种方式：

方式一：

```git
git fetch [remote-name]
git merge [remote-name]/[branch-name]
```

方式二：

因为已经将本地分支与远程分支建立了关联关系，可以直接在当前本地跟踪分支上使用`git pull`命令。它是方式一的简写形式，会自动找到关联的远程分支，拉取最新数据，并合入本地的分支。

```git
git pull
```

#### 分支合并及冲突:

把分支test合并到master。

1、如果是线性合并（fast forward）：直接将master指针移动到对应合并的分支指针位置。

2、如果不是线性合并，合并时会将2个分支的快照进行合并，自动生成一个提交对象（commit object）

```git
git checkout master
git merge test
```

合并时发生冲突解决：

修改文件，添加文件后触发提交

```git
git add [file]
git commit
```

#### 变基操作

使用变基也可以将2个分支合为1个分支，而且提交记录没有分叉结构。使用`git rebase`的要求是没有进行过远程推送，即`git push`的分支，如果进行了远程推送，则不允许使用`git rebase`，否则可能会出问题。`git pull`默认是合并模式，即`git merge`，如果想使用`rebase`模式，可以执行`git pull --rebase`（此时是以`[remote-name]/[branch-name]`分支作为基准的）。

例子：将分支B合入分支A

使用合并的方法：

```git
git checkout A
git merge B
```

使用变基的方法：

```git
git checkout B
# 将A设为基准
git rebase A
# 变基后，会将分支B与分支A的相同祖先之后的提交添加到A指针之后，即没有交叉节点了
# 指针B指向最新的提交
# 再对A进行一次快速合并即可（fast foward）
git checkout A
git merge B
```

上面前2步的简写形式：

```git
git rebase A B
```

不将分支的父节点移动到基准分支后：

```git
# 以A分支为基准，将C分支中与B分支不是共同父节点的提交移动到A分支之后
git rebase --onto A B C
```

## 删除文件

缓存区没有该文件

```git
rm [file]
git rm [file]
git commit
```

缓存区有该文件未提交

```git
rm [file]
git rm -f [file]
git commit
```

删除缓存区和仓库的文件，但保留工作区的文件

```git
git rm --cached [file]
```

## 文件改名/移动

```git
mv file1 file2
```

相当于

```git
mv file1 file2
git rm file1
git add file2
```

## 查看提交记录

```git
git log
```

常用命令：

```git
# 查看所有分支合并情况
git log --graph --all
```

输出格式：

| 选项                | 说明                                                                 |
| ----------------- | ------------------------------------------------------------------ |
| `-p`              | 按补丁格式显示每个更新之间的差异。git log -p 2                                      |
| `--stat`          | 显示每次更新的文件修改统计信息。                                                   |
| `--shortstat`     | 只显示 --stat 中最后的行数修改添加移除统计。                                         |
| `--name-only`     | 仅在提交信息后显示已修改的文件清单。                                                 |
| `--name-status`   | 显示新增、修改、删除的文件清单。                                                   |
| `--abbrev-commit` | 仅显示 SHA-1 的前几个字符，而非所有的 40 个字符。                                     |
| `--relative-date` | 使用较短的相对时间显示（比如，“2 weeks ago”）。                                     |
| `--graph`         | 显示 ASCII 图形表示的分支合并历史。                                              |
| `--pretty`        | 使用其他格式显示历史提交信息。可用的选项包括 oneline，short，full，fuller 和 format（后跟指定格式）。 |

输出查询：

| 选项                    | 说明                |
| --------------------- | ----------------- |
| `-(n)`                | 仅显示最近的 n 条提交      |
| `--since`, `--after`  | 仅显示指定时间之后的提交。     |
| `--until`, `--before` | 仅显示指定时间之前的提交。     |
| `--author`            | 仅显示指定作者相关的提交。     |
| `--committer`         | 仅显示指定提交者相关的提交。    |
| `--grep`              | 仅显示含指定关键字的提交      |
| `-S`                  | 仅显示添加或移除了某个关键字的提交 |

## SSH免密登录

生成自己的`SSH`登录公私钥，将公钥配置到`git`远程仓库上

```git
ssh-keygen -t rsa -C "xx@xx.com"
```

## 参考文献：

[Pro Git](https://www.progit.cn/)
