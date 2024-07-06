# 什么是git
> 参考： [廖雪峰博客-git](https://www.liaoxuefeng.com/wiki/896043488029600)
- 分布式版本管理系统
- git status命令可以让我们时刻掌握仓库当前的状态。
- git diff这个命令看看具体修改了什么内容。
## 工作区
![](https://pic.imgdb.cn/item/66893da3d9c307b7e963aae2.png)

## 版本库
![](https://pic.imgdb.cn/item/66893dc7d9c307b7e963f16a.png)

# git rebase和git merge的区别是什么？
> 参考：
> - [git merge 和 git rebase](https://www.cnblogs.com/michael-xiang/p/13179837.html)
> - [git merge 和 git rebase 的区别](https://blog.csdn.net/u010698107/article/details/129000177)
- Head指向的是commit节点。
- git merge 操作是区分上下文的。当前分支始终是目标分支，其他一个或多个分支始终合并到当前分支。
  - fast-forward 移动指针快速合并
![](https://pic.imgdb.cn/item/66893e37d9c307b7e964ec96.png)
  - no-ff 新生成一个commit节点，合并两个分支的修改
![](https://pic.imgdb.cn/item/66893e7ad9c307b7e9659855.png)
- rebase 合并往往又被称为 「变基」，「变基」就是改变当前分支的起点。注意，是当前分支！ rebase 命令后面紧接着的就是「基分支」。
![](https://pic.imgdb.cn/item/66893ebdd9c307b7e9664601.png)
- git checkout feature ; git rebase master。特性分支 feature 向前移植到了 master 分支。经常使用 git rebase 操作把本地开发分支移植到远端的 origin/<branch> 追踪分支上。也就是经常说的，「把你的补丁变基到 xxx 分支的头」
## 总结
- Git merge 将两个分支中的所有提交都合并到一起，并创建一个新的合并提交，保留了历史记录。这导致了 Git 历史记录中出现多个分支合并点的情况，从而使历史记录更加复杂。
- Git rebase 是将一个分支的提交序列“拉直”，并且将其与另一个分支合并。这意味着，提交历史看起来好像是一条直线，没有分叉，因此整个提交历史看起来更加整洁，历史记录保持相对简单。
# git fetch 和git pull的区别
> 参考：https://github.com/febobo/web-interview/issues/224

![](https://pic.imgdb.cn/item/66893f92d9c307b7e96870ca.png)
- **git pull = git fetch +git merge**
- git fetch 更安全也更符合实际要求，在 merge 前，我们可以查看更新情况，根据实际情况再决定是否合并

# git add 后 git cheout会有什么情况？
- git add但不git commit，也不git stash，直接git checkout到新分支，做修改，然后再git commit的话，记录就在切换后的分支下面。
- 本质原因：**一个本地的git repo只有一个工作区和暂存区，但是有多个分支的提交区**，而我们的checkout只是将HEAD指针从一个分支切换到另一个分支。

# 问题解决
## git删除已经 add 的文件 (如何撤销已放入缓存区文件的修改)
使用 git rm 命令即可，有两种选择:
> 一种是 git rm --cached "文件路径"，不删除物理文件，仅将该文件从暂存区中删除；一种是 git rm --f  "文件路径"，不仅将该文件从缓存中删除，还会将物理文件删除（不会回收到垃圾桶，直接删除）。

修改或新增的文件通过 git add --all命令全部加入缓存区（index区）之后，使用 git status 查看状态
（git status -s 简单模式查看状态，第一列本地库和缓存区的差异，第二列缓存区和工作目录的差异），
提示使用 git reset HEAD <file> 来取消缓存区的修改。
不添加<file>参数，撤销所有缓存区的修改。
另外**可以使用 git rm --cached 文件名 ，可以从缓存区移除文件，使该文件变为未跟踪的状态，同时下次提交时从本地库中删除**。

> 注：
没有带参数的 git reset 命令，默认执行了 --mixed 参数，即用reset版本库到指定版本，并重置缓存区，在上面的命令中指定的目录版本是HEAD，即当前版本，所以实际上没有任何修改，仅是重置了缓存区。
## .gitignore 如何添加已经 commit 过的文件到不追踪规则中]
https://laowangblog.com/gitignore-after-commit.html

1. 删除 track 的文件 (已经 commit 的文件)

```shell
git rm -r --cached xxx
git commit -a -m "删除不需要的文件"
```
2. 在 .gitignore 文件中添加忽略规则

   - 在 .gitignore 文件中添加 ignore 条目, 如: .DS_Store
   - 提交 .gitignore 文件: git commit -a -m "添加ignore规则"
3. 推送到远程仓库让 ignore 规则对于其他开发者也能生效
```shell
git push [remote]
```
## 常用操作
### [生成.ignore文件](https://www.jianshu.com/p/a09a9b40ad20)
首先，在你的工作区新建一个名称为.gitignore的文件。

然后，把要忽略的文件名填进去，Git就会自动忽略这些文件。

不需要从头写.gitignore文件，GitHub已经为我们准备了各种配置文件，只需要组合一下就可以使用了。所有配置文件可以直接在线浏览：https://github.com/github/gitignore

#### Go版本
```shell
# If you prefer the allow list template instead of the deny list, see community template:
# https://github.com/github/gitignore/blob/main/community/Golang/Go.AllowList.gitignore
#
# Binaries for programs and plugins
*.exe
*.exe~
*.dll
*.so
*.dylib

# Test binary, built with `go test -c`
*.test

# Output of the go coverage tool, specifically when used with LiteIDE
*.out

# Dependency directories (remove the comment below to include it)
# vendor/

# Go workspace file
go.work
```

#### **解决.gitignore不生效的问题**
**背景**

有时做项目时，不希望将某些文件上传到gitlab上了，就需要在.gitignore文件中增加不需要上传的文件的目录，这时你再去git status时还是会看到不希望上传的文件做了更改了，这就是.gitignore没有生效。

**解决方法**

其实解决方法也非常简单，我们清理下git缓存，也就是清空暂存区的文件,再更新下.gitignore文件就好：
```shell
删除git 缓存
git rm -r --cached .
git add .
git commit -m 'update .gitignore'
```
下次再提交就不会再看到不希望提交的文件了。

### **提交操作**
1. git add -A
2. git commit -m “...”
3. 配好ssh。git remote add <远程主机命名：origin>  +项目地址
4. git push <远程主机命名：origin>  <远程主机分支命名:master>

### **切换操作**
- git checkout ...

### **merge**
- git merge --no-ff -m "..." dev (不会删除分支信息)
### **其他**
- 删除远程地址git remote remove 

