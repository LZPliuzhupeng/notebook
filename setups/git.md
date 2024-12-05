## git教程
?>[Git教程-廖雪峰](https://www.liaoxuefeng.com/wiki/896043488029600)

## 安装git

[git安装指路](/setups/resource.md)

## 下载项目
打开命令行，切换至目标文件夹，输入    
`git clone 克隆地址`


## 上传项目

上传前要下载git本地仓库，即创建项目后下载项目，    
在文件夹发现一个隐藏的`.git文件`夹即为成功。

```
打开命令符，切换到目标文件夹（要有.git仓库）
1. git add ./   (提交当前文件夹的所有文件)
2. git commit -m "提交所有"  （检查提交文件）
3. git pull （检查提交分支）
4. git push  （提交）
```
> 如果只想上传单个文件    
`git add 本地文件的相对路径`


## git多人开发

1、组长创建项目，所有人克隆地址下载本地仓库。

2、组长添加开发者或管理者，发送链接，成员打开链接即已加入。  
  
* 管理->项目成员管理->开发者->添加项目成员->链接邀请

3、查看本地仓库的项目

`git remote -v`


4、创建分支

`git branch 分支名字 `


5、查看本地所有分支

`git branch`
前面带*星，当前选择的分支


6、切换分支

`git checkout 分支名字`

创建并切换dev分支

`git checkout -b dev `



7、推送分支与文件

添加文件    
`git add ./txt_zhupeng.txt`


准备提交，添加备注    
`git commit -m "提交"`


与线上仓库对比    
`git pull`
> 注意：如果第一次推送次分支，会出现`There is no tracking information for the current branch.`
线上没发现你所推送的分支，这种情况直接跳过`git pull`步骤


推送分支下的文件    
`git push origin 分支名字`


8、将分支合并到master并再次提交

切换到master分支上:
`git checkout master`


`git merge --no-ff -m "合并至master分支" 分支名字`

再次提交
 ```
git add ./

git pull（将其他成员的代码，拉倒本地，再提交自己的代码）

git push
```


9、删除分支
`git branch -d 分支名`
> 删除前请谨慎思考


## git的更多使用
分支分为两种,本地分支 `local branches` 和 远程跟踪分支`Remote-tracking branches` 
``` git
# 显示本地分支
git branch
# 显示远程分支
git branch -r
# 显示所有本地和远程分支
git branch -a

```
## 基于远程跟踪分支创建本地分支
如果你想基于远程跟踪分支创建本地分支（在本地分支上工作）,你可以使用如下命令：`git branch –track`或 `git checkout –track -b` ，两个命令都可以让你切换到新创建的本地分支。例如你用 `git branch -r` 命令看到一个远程跟踪分支的名称为 `origin/refactored` 是你所需要的，你可以使用下面的命令：
```git
git checkout --track -b refactored origin/refactored
```
在上面的命令里，`refactored `是这个新分支的名称，`origin/refactored` 则是现存远程跟踪分支的名称。（在 `git` 最新的版本里，例子中 `-track` 选项已经不需要了，如果最后一个参数是远程跟踪分支，这个参数会被默认加上。）

## 如果你想把shuai分支的内容合并到melon分支
- 首先切换到shuai分支,然后git pull 下来
- 第二切换到melon分支,然后执行git merge shuai
- 最后执行 git push 上传到melon远程

## 其他命令
```git
# 查看状态,也可以查看当前分支
git status

# 切换到指定分支，并更新工作区
git checkout [branch-name]

# 切换到上一个分支
git checkout -

# 新建一个分支，但依然停留在当前分支
git branch [branch-name]

# 新建一个分支，并切换到该分支
git checkout -b [branch]

# 建立追踪关系，在现有分支与指定的远程分支之间
git branch --set-upstream [branch] [remote-branch]
# 将本地创建的分支上传到远程
git push --set-upstream origin shuai  

# 删除本地分支,不能在自己分支下删除自己所在的分支,只是删除本地分支,线上的还存在
git branch -d [branch-name]

# 删除远程分支
git push origin --delete [branch-name]
git branch -dr [remote/branch]

# 重置当前分支的HEAD为指定commit，同时重置暂存区和工作区，与指定commit一致,即恢复操作
git reset --hard [commit]

# 显示暂存区和工作区的代码差异
git diff

# 显示某次提交的元数据和内容变化
git show [commit]

# 生成一个可供发布的压缩包
git archive
```

## 可视化工具
?> 推荐使用[GitHub Desktop](https://desktop.github.com/)