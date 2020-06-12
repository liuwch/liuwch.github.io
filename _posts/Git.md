[TOC]

# 创建版本库
> 版本库又名仓库，英文名repository

## 1.初始化 
在某个文件夹下运行init，这个文件夹可以是空文件夹或者是非空的。
```
git init
```
## 2.把文件添加到仓库

1. 新建一个`readme.md` 内容如下
```
11111

```
2. `git add readme.md`
3. `git commit -m "wrote a readme file 11111"`
    <br>`git commit`命令`-m`后面是本次提交的说明
### add多个文件ls
如果有很多改动可以通过 `git add -A .`来一次添加所有改变的文件。

注意 `-A `选项后面还有一个句点。` git add -A`表示添加所有内容

`git add . `表示添加新文件和编辑过的文件不包括删除的文件;

`git add -u` 表示添加编辑或者删除的文件，不包括新添加的文件。



# 版本退回
> 不断对文件进行修改，然后不断提交修改到版本库里，每当你觉得文件修改到一定程度的时候，就可以“保存一个快照”，这个快照在Git中被称为commit。一旦你把文件改乱了，或者误删了文件，还可以从最近的一个commit恢复，然后继续工作，而不是把几个月的工作成果全部丢失。
> 

再次修改`readme.md`中的内容，如下：
```
11111
22222
```
然后`commit`
```
git add readme.md
git commit -m "add 22222"
```

---

第三次修改`readme.md`
```
11111
33333
```
然后`commit`
```bash
git add readme.md
git commit -m "remove 22222 and add 33333"
```

---
**所以现在一共有3个版本在仓库中**

版本1：
```
11111
```
版本2：
```
11111
22222
```
版本3：
```
11111
33333
```
## 版本信息查看
在Git中，我们用`git log`命令查看
```bash
Author: JDYG <gao321282@icloud.com>
Date:   Mon Mar 25 23:02:58 2019 +0800

    remove 22222 and add 33333

commit b3e5173988208aa642918be116eb9a5cc6122899
Author: JDYG <gao321282@icloud.com>
Date:   Mon Mar 25 22:59:07 2019 +0800

    add 22222

commit 6b4676c6ba3c91f76d05af36806f632cb8ebb000
Author: JDYG <gao321282@icloud.com>
Date:   Mon Mar 25 22:46:05 2019 +0800

    add a readme file 11111

```
`git log`命令显示从最近到最远的提交日志 。如果嫌输出信息太多，看得眼花缭乱的，可以试试加上`--pretty=oneline`参数
```
git log --pretty=oneline
```

## 回退到历史版本
首先，Git必须知道当前版本是哪个版本.
<br>在Git中，用`HEAD`表示当前版本，也就是最新的提交.
<br>上一个版本就是`HEAD^`，上上一个版本就是`HEAD^^`.
当然往上100个版本写100个^比较容易数不过来，所以写成`HEAD~100`。


现在，我们要把当前版本`remove 22222 and add 33333`**回退到上一个版本**`add 22222`，就可以使用git reset命令：
```bash
git reset --hard HEAD^
```

`--hard`参数的含义下文会讲

现在的log里面只有`11111`和`22222`,**只要git窗口没有关闭**就能回退到`33333`
```bash
git reset --hard 版本的索引
```
**如果git窗口关闭了**，可以使用`git reflog`来查看历史的命令记录从而得到历史版本号，然后在用`git reset`回退到历史版本


# 文件操作
## 文件退回
1. 当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，
使用`git status`,会提示用命令`git checkout -- file`
2. 当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令`git reset HEAD <file>`就回到了场景1，第二步按场景1操作。
3. 已经提交了不合适的修改到版本库时，想要撤销本次提交，参考版本回退一节，不过前提是没有推送到远程库。

## 文件删除
当你要删除文件的时候，可以采用命令：`rm test.txt`

这个时候（也就是说这个时候只执行了`rm test.txt`）有两种情况

1. 第一种情况:的确要把test.txt删掉，那么可以执行`git rm test.txt`,`git commit -m "remove test.txt"`
然后文件就被删掉了

2. 第二种情况:删错文件了，不应该删test.txt，注意这时只执行了`rm test.txt`，还没有提交，所以可以执行`git checkout test.txt`将文件恢复。

并不是说执行完`git commit -m "remove test.txt"`后还能用checkout恢复，commit之后版本库里的文件也没了，自然没办法用checkout恢复

---

`git checkout`其实是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“一键还原”。

# 远程库
## 添加到远程库
1. 新建一个远程库
2. 把本地仓库的内容推送到GitHub仓库。
```
git remote add origin https://github.com/JDYG/atest.git
git push -u origin master
```
远程库的名字就是`origin`，这是Git默认的叫法，也可以改成别的，但是`origin`这个名字一看就知道是远程库。

3. 此后，每次本地提交后，就可以使用命令`git push origin master`推送最新修改；

**所以综合以上分析将修改的后文件`add`,`commit`,`push`的总流程如下**

```
git add -A .   #一次添加所有改变的文件  注意 -A选项后面还有一个句点
git commit -m "messages"
git push origin master
```


# 分支管理

## 创建与合并分支
**1.** 我们创建`dev`分支，然后切换到`dev`分支：
```
git checkout -b dev
```
`git checkout`命令加上`-b`参数表示创建并切换，相当于以下两条命令：
```
git branch dev
git checkout dev
```
**2.** 用`git branch`**查看当前分支**
<br>`git branch`命令会列出所有分支，当前分支前面会标一个*号。

**3.** 然后，我们就可以在`dev`分支上正常提交
```
git add readme.md
git commit -m "branch dev"
```

**4.** 现在，dev分支的工作完成，我们就可以切换回master分支：
```
git checkout master
```
切换回master分支后，再查看一个readme.md文件，刚才添加的内容不见了！因为那个提交是在dev分支上，而master分支此刻的提交点并没有变.

**5.** 现在，把dev分支的工作成果合并到master分支上：
```
git merge dev
```
`git merge`命令用于合并指定分支到当前分支。

**6.** 合并完成后，就可以放心地删除dev分支了
```
git branch -d dev
```

## 合并远程分支
```
git fetch --all    //只是下载代码到本地，不进行合并操作
git reset --hard origin/master //把HEAD指向最新下载的版本
```
`git pull`：相当于是从远程获取最新版本并merge到本地
```
git pull origin master
```
上述命令其实相当于`git fetch` 和 `git merge`在实际使用中，
`git fetch`更安全一些，因为在merge前，我们可以查看更新情况，然后再决定是否合并

```
git pull <远程主机名> <远程分支名>:<本地分支名>

#比如，要取回origin主机的next分支，与本地的master分支合并，需要写成下面这样 -

git pull origin next:master
```

# 标签管理
## 创建标签
**1.** 首先，切换到需要打标签的分支上：
```
git checkout dev
```
**2.** 然后，敲命令`git tag <name>`就可以打一个新标签，默认是最新的commit：
```
git tag v1.0 [制定的ID]
```
用`git tag`查看所有标签

还可以创建带有说明的标签，用`-a`指定标签名，`-m`指定说明文字：
```
git tag -a v0.1 -m "version 0.1 released" 1094adb
```
## 删除标签
```
git tag -d v1.0
```
因为创建的标签都只存储在本地，不会自动推送到远程。所以，打错的标签可以在本地安全删除。

## 推送标签
如果要推送某个标签到远程，使用命令`git push origin <tagname>`：
```
git push origin v1.0
```
或者，一次性推送全部尚未推送到远程的本地标签：
```
git push origin --tags
```
## 删除远程标签

如果标签已经推送到远程，要删除远程标签就麻烦一点，先从本地删除：
```
git tag -d v1.0
```
然后，从远程删除。删除命令也是push，但是格式如下：
```
git push origin :refs/tags/v1.0
```



## 忽略文件

创建$.gitignore$, 忽略的文件如下格式：

```
/**/*.png
/**/*.pfm
/__pycache__/*
/.vscode
```

但是有些时候有些工程文件夹种的代码文件和工程文件混杂在一起，这时需要忽略某个文件夹，却不能忽略某个文件夹中的代码文件。例如有个工程目录为 `aaa`, 其中的文件夹结构如下:
```shell
aaa
-------------/bbb
-------------/bbb/main.cpp
-------------/bbb/PFMReadWrite.h
-------------/bbb/PFMReadWrite.cpp
-------------/bbb/aaa.vcxproj
-------------/aaa.vcxproj.filters
-------------/aaa.sln
-------------/x64
```

这是只需要对其中的`mian.cpp`, `PFMReadWrite.h`, `PFMReadWrite.cpp`做git, 而其他的文件都可以忽略。正确的写法如下：

```shell
/aaa/*              #不要漏掉*  表示忽略/aaa文件夹内所有
!/aaa/bbb           #不忽略/aaa/bbb文件夹
/aaa/bbb/*          #忽略/aaa/bbb文件夹内所有
!/aaa/bbb/*.cpp     #不忽略/aaa/bbb文件夹内的.cpp文件
!/aaa/bbb/*.h
```

