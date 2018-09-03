# git基础

## 配置

    // 设置全局用户信息
    git config --global user.name "freedomcly"
    git config --global user.email freedomcly@gmail.com

> 如何多账户切换？
> 假设一台电脑的不同项目要使用不同的 git 账号，可以配置一个比较常用的 --global 账号，然后在需要使用其他账号的项目下不加 --global 参数来配置项目内的 git 账号。

## 开发时常用操作

以下操作可以满足简单的本地文件版本管理。

    // 初始化已有数据的项目，开始用 git 追踪
    git init
    // 克隆项目
    git clone [url]

    // 当前状态
    git status
    // git status 的简略版
    git status -s

    // 添加到暂存区域
    git add [文件路径]

    // 已修改文件与暂存区文件的差异比较
    git diff
    // 暂存区文件与已提交区文件的差异比较
    git diff --staged

    // 提交
    git commit -m "something"
    // 跳过 git add 并提交
    git commit -a -m "something"

> 文件在 git 内有哪些状态？
> 对于任何一个文件，在 git 内都只有三种状态：已修改（modified），已暂存（staged）和已提交（committed）。
> 已修改→已暂存（git add）
> 已暂存→已提交（git commit）

## 历史记录

    // commit 历史记录
    // 可以根据哈希、作者、时间、代码关键字、文件路径等来查询提交
    git log

    // 最近两次 commit 的内容详细差异
    git log -p -2

    // 每次 commit 的文件信息
    git log --stat

    // 用合适的方式输出 log ，比如 oneline/format，format 需要加参数
    git log --pretty=oneline
    git log --pretty=format:"%h - %an, %ar : %s"

    // 用图形方式展示，像 sourcetree 一样
    git log --pretty=oneline --graph 

> 命令行的 git 和图形化的 git 有什么关系？
> 图形化的 git 是命令行 git 的子集。曾以为图形化 git 比命令行 git 的优势在于可以让分支可视化，但其实命令行的 git log 完全可以满足 sourcetree 这样的图形工具的功能，而 bitbucket / gitlab 之类的工具也仅仅是方便大家互相 review 和 merge 代码。

## 分支

分支是目前 git 中最常用的功能，极大地方便了日常程序维护与开发。
可以随时以不同的基准建立分支，同一时间在不同分支间切换，也就是在不同任务间切换，既可以修线上的 bug，也可以修开发中的 bug，还可以继续开发。

    // 查看本地分支
    git branch

    // 删除本地分支，如果分支没有被 merge 则提示
    git branch -d [分支名]
    // 强制删除本地分支，如果分支没有被 merge 不提示
    git branch -D [分支名]

    // 已经 merge 到当前分支的分支
    git branch --merged 
    // 未merge到当前分支的分支
    git branch --no-merged

    // 切换分支
    git checkout [分支名]
    // 切换到新建分支
    git checkout -b [新分支名]

    // 合并分支，把某分支合并到当前分支
    git merge [分支名]
    // 分支衍合
    // 分支衍合的结果与合并相同，但历史记录不同
    // git pull -r
    git rebase

    // 把分支推送到远程仓库
    git push [远程名] [本地分支名]
    git push [远程名] [本地分支名]:[远程分支名]

    // 删除远程分支，可以这样理解：用空白来代替远程分支
    git push [远程名] :[远程分支名]

    // 从远程仓库抓取数据到本地，但不会 merge 分支
    git fetch origin

> 如何合并特定的 commit 到另一个分支
> https://ariejan.net/2010/06/10/cherry-picking-specific-commits-from-another-branch/

> merge 和 rebase 的区别
> 一言以蔽之，merge 是三方合并（两个分支的共同祖先和这两个分支），rebase 提取了一个个的 commit，把它们移动到当前分支。rebase 可以看做是 cherry-pick 的集合。
> https://www.jianshu.com/p/c17472d704a0

## 远程仓库

    // 创建远程仓库
    git remote add [shortname] [url]

    // 从远程仓库抓取数据到本地
    git fetch [remote-name]

    // 向远程仓库提交数据
    git push [remote-name] [branch-name] 

    // 从远程仓库抓取数据并 merge 到当前分支
    git pull [remote-name] [branch-name]

    // 查看远程仓库详细信息
    git remote show [remote-name]

    // 修改远程仓库名称
    git remote rename [old-name] [new-name]

    // 删除远程仓库
    git remote rm [remote-name]

> 在什么情况下，同一个项目需要多个远程仓库？

## 标签

每次发布版本可以给当前位置打标签。

    // 显示现有标签
    git tag

    // 添加含附注的标签
    git tag -a [tagname] -m "something"

    // 添加轻量级标签
    git tag [tagname]

    // 后期补标签，通过校验和
    git tag -a v1.2 9fceb02 后期加注标签，校验和

    // 显示标签信息
    git show [tagname]

    // 把标签推送到远程
    git push origin [tagname]

## 其他技巧

(1) tab 自动补全
输入不完全的命令，两次 tab，可以选择可能的命令。

(2) git 命令别名
把 git 默认命令改成更简短的名称。

    git config --global alias.co checkout
    git config --global alias.br branch
    git config --global alias.ci commit
    git config --global alias.st status

    git config --global alias.unstage 'reset HEAD --'
    git config --global alias.last 'log -1 HEAD'

(3) 维护 .gitignore 文件
忽略开发工具、编译生成的文件等。

## 恢复数据

    // 恢复已修改未暂存的文件
    git checkout [文件路径]

    // 恢复已暂存的文件
    git reset HEAD [文件路径]

    // 修改上一次提交
    git commit --amend

    // 恢复到任意 commit
    // 也可以恢复到任意远程分支的任意文件
    git checkout [哈希值]

## 开发流程

多人同时操作一个分支时，如果一个人 push 过，另一个人就要先 pull 再 push，这样很麻烦。如果是各自有不同的分支，提交后再在远程merge到主分支，就不容易出现那种情况。

(1) 两个主分支：master、develop
master 为上次已发布的稳定版本，作为修线上 bug 的基准分支。
develop 为当前开发中的新版本，任何开发完成的 feature 就可以 merge 到这个分支。

(2) 开发新功能时从 develop 分支 checkout 一个新分支 feature/[feature-name] ，修线上 bug 时从 master 分支 checkout 一个新分支 fixbug/[bug-name]。

## 参考

* [git-scm](https://git-scm.com/)