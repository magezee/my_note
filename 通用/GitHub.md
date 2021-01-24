# GitHub

## Git的使用

### 介绍

git工作区有一个隐藏目录`.git`，这个不算工作区，而是Git的版本库。

Git的版本库里存了很多东西，其中最重要的就是称为stage（或者叫index）的暂存区，还有Git为我们自动创建的第一个分支`master`，以及指向`master`的一个指针叫`HEAD`

一个分支里有多个版本（多次commit的结果），更改HEAD的指针达到寻找到历史版本

```
用git add把文件添加进去，实际上就是把文件修改添加到暂存区
用git commit提交更改，实际上就是把暂存区的所有内容提交到当前分支
```

<img src="https://img-blog.csdnimg.cn/20200430110926541.png" style="float:left">

-----

### 上传文件

Git的工作区域分为三个
    工作区  （Working Directory）
        添加 编辑 修改文件等动作
    暂存区
        暂存已经修改的文件最后统一提交到git仓库中
    Git Repository  （Git仓库）
        最终确定的文件保存到仓库 成为一个新的版本 并且对他人可见

刚安装完git时需要初始化个人信息
    git config --global user.name +github用户名
        设置用户名
    git config --global user.email +邮箱
        设置用户名邮箱
    git config --list
        查看git配置

```
最终push提交的是暂存区里的文件
放到暂存区的文件再进行修改，实际上是在工作区的修改，若不再次将修改后文件add提交到暂存区，则push不会看到修改变化
```

**指令**

```
git init 	     		  	
	在当前目录创建仓库（生成.git隐藏文件）

git status 		  		  		
	查看当前文件在哪个工作区，只会显示工作区和暂存区的文件
    
git add +文件名		   		 
	将文件从工作区提交到到暂存区

git add --all 或 git add .
	将当前文件夹内所有文件添加到暂缓区

git commit -m +提交描述		
	将文件从暂存区提交到仓库
	
git push
	将本地仓库提交到远程仓库，若没有成功提交 可以尝试 git pull再提交或强制提交 git push -f
	
git clone +仓库地址
	将远程仓库（Github对应的项目）复制到本地
	
git reset HEAD 
	将暂存区的文件退回到工作区（注意一定要用HEAD表示当前版本）

git reset HEAD +fileName
	将暂存区的指定文件退回到工作区

git checkout -- +fileName	（如：git checkout -- readme.txt）
	将指定文件回到最近一次git commit 或 git add 状态（回滚文件，撤销修改），但是如果已经push提交的话就要进行版本回滚了
```

**设置远程仓库**

方式一：

```
git remote add origin + 远程仓库地址
```

设置完成后第一次上传需要配置主分支

```
git push -u origin master
```

方式二：

直接在`.git`里的 `config` 文件加上配置项，上面命令行配置完后的config文件内容也会变成下面这样

```
[remote "origin"]
	url = 仓库地址
	fetch = +refs/heads/*:refs/remotes/origin/*
	
[branch "master"]
	remote = origin
	merge = refs/heads/master
```



----

### 回滚

在Git中，用`HEAD`表示当前版本，上一个版本是`HEAD^`，上上个是`HEAD^^`，再往上时就用`~`表示，如往上100个版本是`HEAD~100`

```
git log （git log --pretty=oneline 只看commit省略杂余信息）
	查最近3次commit的记录 

git reflog
	查看记录的每一次命令

git reset --hard HEAD^
	使用相对版本回到到上一个版本，当回滚到之前的版本时，要想重新回到未来的版本，需要使用指定版本号
	
git reset --hard f12c6
	使用版本号回到指定版本（版本号可在git log中查看，一般只写前几位就行）
```

```
$ git log --pretty=oneline
f12c63e66160ac354849a508ba7a52fe44d696c4 (HEAD -> master, origin/master, origin/HEAD, 01) add_routes
6f4a7c3cb00aa7cc24ed0503664b9d7b8eb9a907 add_routerg
```

用`git log`可以查看提交历史，以便确定要回退到哪个版本

用`git reflog`查看命令历史，以便确定要回到未来的哪个版本



-----

### 分支

每一种版本控制系统都以某种形式支持分支，使用分支意味着你可以从开发主线上分离开来，然后在不影响主线的同时继续工作

Git 会用该分支的最后提交的快照替换你的工作目录的内容， 所以多个分支不需要多个目录

**指令**

```
git branch
	查看所有分支（使用git init时默认创建了一个master分支）

git branch +branchName	（git branch -b +branchName 创建分支并切换到该分支）
	创建指定分支
	
git checkout +branchName 
	切换到指定分支		（创建+切换 git checkout -b +branchName 或 git switch -c +branchName）
	
git switch +branchName
	切换到指定分支（推荐使用这个，因为checkout不仅可切换也可以用来撤消修改文件，会混淆）

git merge +barnchName
	将指定分支合并分支到当前分支，指定分支的内容会覆盖当前分支
	
git branch -d branchName
	删除分支（处于该分支时不可删除该分支）
	
```

**分支管理**

在实际开发中，我们应该按照几个基本原则进行分支管理：

- `master`分支应该是非常稳定的，也就是仅用来发布新版本，平时不能在上面干活；

- 干活都在`dev`分支上，也就是说，`dev`分支是不稳定的，到某个时候，比如1.0版本发布时，再把`dev`分支合并到`master`上，在`master`分支发布1.0版本；

- 每个人都在`dev`分支上干活，每个人都有自己的分支，时不时地往`dev`分支上合并就可以了。

所以，团队合作的分支看起来就像这样：

**使用分支例子**

创建分支：

```
先更新保证代码最新
	git pull	或者 git reset --hard HEAD
创建分支
	git checkout -b barnchName
推送到远程仓库，保证分支名一致
	git push origin branchName:branchName
```

切换分支：

```markdown
切换分支时需要注意的：
	当想切换分支内容发生更改时，需要前分支 git commit 后切换才可以保证更改的代码已被保存在前一条分支中切切换后对下一条分支不会造成影响
```

新建分支：

```markdown
新建分支时需要注意的：
	当目前所在分支时a分支，创建b分支时，b分支的初始内容是a分支最新一次commit的内容
	因此同一个项目中，为了不互相混淆，一般都会先切回master分支再新建分支
```





------

### 克隆与提交

```
在仓库里clone下来的项目
可通过再次add commit push 来上传最新版本
这样再次clone时则自动下载最新版本
若想下载以前版本 可在github仓库的commit选项中看到历史commit并提交的文件，在这下载
```

克隆下来默认显示master分支的最新commit项目，若想切换到其他分支（如dev）的项目，需要在克隆项目中创建与远程项目一样的分支，并且切换到该分支，本地创建并切换到一个新的分支dev，然后再将github上的origin/dev分支的代码给复制过来

若分支名字不同，更新上传时会遇到麻烦

```
git branch -a 	查看远程仓库的所有分支
git checkout -b dev origin/dev	创建dev分支并切换到该分支，且将远程仓库的dev分支内容复制进来
```

<img src='https://img-blog.csdnimg.cn/20200430153138546.png' style="float:left">



----

### 更新本地代码

```
git fetch 	获取远程最新代码版本
git pull 	拉取远程最新版本的代码
```

`git fetch` 主要用于在用户检查了文件变动后决定是否合并到工作机的本分支中

`git pull` 是将远程仓库的最新内容下载下来并直接合并，`git pull = git fetch + git merge`，这样有可能会产生冲突，因此一般都是先使用 `git fetch` 再使用`git pull` 来达到更新本地代码的效果

注意的是，这两个命令拉取的是远程仓库上的所有分支，如果只想单独拉取某个分支，可以这么写

```
git fetch origin dev:dev	获取远程dev分支，存在本地分支dev（无分支自动创建）
git merge dev				合并dev的内容
```

```
git pull origin dev:dev		获取远程dev分支，与本地dev分支内容合并
```



---

### tag的使用

如果想记录笔记，让每一次项目的内容互不干扰，可以使用tag（使用分支也可但是会比较麻烦）

思路是：`原始项目内容 → 项目笔记1 → 提交tag1 → 回退至原始项目 → 项目笔记2 → 提交tag2 `

这样tag1的项目代码和tag2是不会有任何关系的

**过程**

提交初始代码

```
git add .
git commit -m '初始化'
git push
```

增加项目一代码并添加tag（可以使用 `git tag` 查看所有tag）

```
git add .
git commit -m '项目一'
git tag 项目一
```

回退至初始版本

如果回退过程中出现 `Unstaged changes after reset`，可以执行 `git stash` 然后再回退

```
git log（查看初代版本号）		
git reset +初代版本号
```

增加项目二代码并添加tag

```
git add .
git commit -m '项目二'
git tag 项目二
```

推送至远程仓库

```
git push --tags
```

<img src="https://img-blog.csdnimg.cn/20201027165855171.png" style="margin:0">

之后就可以自由使用命令来切换

```
git checkout +tag名字
```



--------

## GitHub操作

### 在github预览网页

可以实现css和js的外部导入

- 将需要预览的网页和所需静态文件上传到github仓库
- 进入该仓库，选择`Settings`，选择`Options`选项，往下拉至`GitHub Pages`，点击`Choose a theme`，选择任意主题（推荐`MINIMAL`）

- 重新拉到`GitHub Pages`，可看到预览链接，如：`https://magezee.github.io/cssTest/`

- 在该链接后加上仓库文件路径即可预览html文件，如：`https://magezee.github.io/cssTest/1/index.html`


