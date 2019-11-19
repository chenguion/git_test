# 基本操作
- 创建版本仓库
	- `git init`
	
- 版本创建
	- `git add 文件或目录`
	- `git commit -m '版本说明信息'`
	
- 查看版本记录
	- `git log`
	- `git log --pretty=online`
	- `git log --graph --pretty=online`
	
- 版本回退
	- `git reset --hard HEAD^`  HEAD指向当前版本
	- `git reset --hard 版本序列号`
	
- 查看操作记录

	- `git reflog`
	
- 工作区、版本库和暂存区
	- 编辑文件都是在工作区
	- git add 是把工作区的修改放入暂存区
	- git commit 是把暂存的区修改一次性做一次版本记录

- 管理修改
	- git commit只会把暂存区的修改提交到版本记录中

- 撤销修改
	- 直接丢弃工作区的改动 `git checkout -- 文件`
	- 修改已经添加到暂存区，但未commit
		- `git reset HEAD 文件`
		- `git checkout -- 文件`
	- 已经commit
		- 版本回退
		
- 对比文件的不同
	- 对比工作区和版本区某个文件 `git diff HEAD -- 文件`
	- 对比两个版本中的文件 `git diff HEAD HEAD^ -- code.txt`

- 删除文件
	- `rm 文件`
	- `git rm 文件`
	- `git commit`
	

# git分支管理

- 查看分支
```
git branch
```
- 创建并切换分支
```
git checkout -b dev
```
```
* dev
  master
```
- 工作完成切换回 master 分支
```
git checkout master
```
- 将dev分支合并回master
```
先切换会master分支
git merge dev
```
```
更新 54d442e..bdbb115
Fast-forward
 note.md | 10 ++++++++++
 1 file changed, 10 insertions(+)
 create mode 100644 note.md
``` 
**注意到Fast-forward信息，Git告诉我们，这次合并是"快速模式"，也就是直接把master指向dev的当前提交，所以合并速度非常快**

- 删除dev分支
```
git branch -d dev
```
---







---
### 有时候合并分支并不总是成功的
---

**两个分支都有了新的提交并且修改的是同一个文件**
```
(base) cgion@Cgion:~/Codelabs/git_test$ git merge dev 
自动合并 code.txt
冲突（内容）：合并冲突于 code.txt
自动合并失败，修正冲突然后提交修正的结果。
```
**这时候就需要手动解决冲突问题**
`然后提交 冲突解决`
```
git log --graph --pretty=oneline
```
```
*   7ab351391689d99a64826dbff049ccdca201e4b5 (HEAD -> master) 解决冲突
|\  
| * f5f5c24e5dbc86d139c417523809400633ff20fa (dev) dev分支提交
* | cb0a6e4006febdd3eedc2206b18f1ae149d4bff7 master提交信息2
|/  
* 578ff7700402a8dcb524bec08a4cf4757c3a8c8f master提交
* bdbb115d97e1db3cd3c6932ce83634f800b5f0a8 note提交1
* 54d442ec6c42127b7ce1c290e4d720988fa2f525 删除code2
* 672cceb54669b73bd3ec41588aceb1b5c28e4001 版本4
* 99fc5a2f50572985f9f44b67ce403322ca5cee27 版本3
* 879706b2a63fe08facb6a327a628845d74b72c75 版本2
* 823b1fd345ca053361dd4a0ffb09e15834fdb22b 版本1

```
**可以查看分支合并流程**

-----

**通常，合并分支时，如果可能，git会用fast forward模式，但是有些快速合并不能成功而且合并时没有冲突，这时会合并之后再做一次新的提交，**但这种模式下，删除分支之后，会丢掉分支信息

--------
- 案例
	- 创建切换到dev分支下
	
	- 新建一个文件code3.txt 编辑内容如下，并提交一个commit
	- 切换回master分支，编辑code.txt并进行一个提交
	
	- 合并dev分支的内容到master分支
	- 出现提示，不能快速合并，所以git提示输入合并说明信息，输入之后合并内容之后git会自动创建一次新的提交
	
	- 使用分支合并命令查看分支信息
	- 删除dev分支
	
	
	
---
**如果要强制禁用fast forward模式，git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息。**
#### `git merge --no-ff -m '禁用fast forword合并' dev`
#### **创建一个新的commit，加上-m参数，把commit描述写进去**
----
- 案例
	- 创建并切换到dev分支
	- 修改code.txt内容，并提交一个commit
	- 切换回master分支
	- 准备合并dev分支，使用禁用fast forword形式
	- 合并后，查看分支历史，不使用fast forword模式，merge后的样式

---

---
### **Bug分支**

**在git中每个bug都可以通过一个新的临时分支来修复，修复后，合并分支，然后将临时分支删除**

- 思路
	- 当你接到一个修复一个代号为001的bug的任务时，很自然地，你想创建一个分支bug-001来修复它，但是，当前正在dev上进行的工作还没有提交，工作值进行到一半无法提交，预计完成还需要1天，但2个小时内必须修复该bug，怎么办？
	- git还提供了一个stash功能，可以把当前工作现场“储藏”起来，等以后恢复现场后继续工作
	- 首先确定要在哪个分支上修复bug，假定需要在master分支上修复，就从master创建临时分支：
	- 修复bug，然后提交
	- 修复完成后，切换到master分支，并完成合并，最后删除bug-001分支

- 相关命令
	- `git stash`  保存工作现场
	- `git stash list`
	- `git stash pop` 切换回工作现场  
- 操作
	-切换到bug所在分支，并创建切换到一个临时分支，修复bug，修复完成之后，切换回bug所在分支并合并领事分支的内容，合并使用no-ff模式
	- 删除临时分支
	-切换回工作分支



# github使用教程
- 步骤
	- 注册github

	- 创建仓库

	- README.md、.gitignore
	
---

- 添加ssh账户
	- 进入github的setting
	- 获取本地电脑的ssh公钥
		- 配置 `.gitconfig`写入email和name
		
	- 使用`ssh-keygen -t rsa -C "邮箱地址"`生成ssh密钥
	- 查看公钥 `cat id_rsa.pub`

-----

- 克隆项目`git clone git@github.com:chenguion/git_test.git`

- 创建分支进行开发
- 开发完成后，向github推送分支
`git push prigin 分支名称`
**这样git就会把该分支退送到远程库对应的远程分支上**

- 将本地分支跟踪服务器分支
`git branch --set-upstream-to=origin/远程分支名称 本地分支名称`
```
分支 'smart' 设置为跟踪来自 'origin' 的远程分支 'smart'。
```
**之后推送分支只需要`git push`**

-----

- 从远程分支上拉取代码
`git pull origin 分支名称`
**使用上述命令会把远程分支smart上的代码下载并合并到本地所在分支。**

----

####工作使用git
- 项目经理：
	- 项目经理搭建项目的框架。
	- 搭建完项目框架之后，项目经理把项目框架代码放到服务器。
- 普通员工：
	- 在自己的电脑上，生成ssh公钥，然后把公钥给项目经理，项目经理把它添加的服务器上面。
	- 项目经理会给每个组员的项目代码的地址，组员把代码下载到自己的电脑上。
	- 创建本地的分支dev,在dev分支中进行每天的开发。
	- 每一个员工开发完自己的代码之后，都需要将代码发布远程的dev分支上。


- Master：用户保存发布的项目代码
- Dev：保存开发过程中的代码

