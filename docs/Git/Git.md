> *Git*

> - ## *提交到master*

```bash
git add  文件夹/
git commit -m "描述"
git remote add 别名 github网址
git push -u	别名 master
```



> - ## *提交到分支并合并*

```bash
git checkout -b 分支名							创建分支				
git add	文件夹/
git commit -m "描述"
git merge 分支名
git push -u	别名 master
git branch -d cate							   删除分支			
```





> - ## *常用命令*

```bash
配置
git config --global user.name 用户名	设置用户签名
git config --global user.email email地址	设置用户email地址

获取与创建项目	
git init	初始化本地库
git clone 远程库地址	从远程库克隆到本地

基本	
git status	查看本地库状态
git add 文件名	添加变动文件到暂存区
git add .	添加当前目录下所有变动文件到暂存区
git restore --staged 文件名	复位在暂存区的文件（add反悔药）
git rm --cached 文件名	移除在暂存区的文件（add反悔药）（同上一条）
git commit -m “备注文本” 文件名	提交暂存区文件到本地库（文件名缺省时，将暂存区所有文件提交）

分支与合并	
git branch	列出所有分支
git branch 分支名	创建分支
git checkout 分支名	切换分支

共享与更新项目	
git remote add 别名 远程仓库地址	添加远程库
git push 远程库地址或其别名 分支名	推送到远程库
git pull 远程库地址或其别名 分支名	同步远程库到本地

检查与比较	
git log	显示当前分支所有提交过的版本信息

管理	
git reflog	可以查看所有分支的所有操作记录（包括已被删除的commit记录和reset的操作，git log所不能）
```



> ## 开发分支（dev）上的代码达到上线的标准后，要合并到 master 分支



`git checkout dev`
`git pull`
`git checkout master`
`git merge dev`
`git push -u origin master`

> ## 当master代码改动了，需要更新开发分支（dev）上的代码

`git checkout master` 
`git pull` 
`git checkout dev`
`git merge master` 
`git push -u origin dev`

