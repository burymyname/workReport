# git命令
## 基本命令
**设置个人信息**
```
git config --global user.name "xxxx"
git config --global user.email "xxx@xxx.com"
```

**创建版本库**
在文件夹总目录下
```
git init
```

**添加文件到仓库**
```
git add [文件名] 
git add . #添加目录下所有文件
```

**提交仓库**
```
git commit -m ["提示信息"]
```

**状态，差别，日志**
```
git status
git diff
git log

git diff HEAD -- [文件名]
```

**版本回退**
```
git reset --hard HEAD^ #指向上一个版本
git reset --hard HEAD~100 #前100个版本
git reset --hard [commit id]
```

**所有历史命令**
```
git reflog
```

**工作区和暂存区**

工作区：工作目录

版本库：`.git`文件就是版本库，里面有暂存区`stage`，HEAD指针
`git add`添加文件到暂存区，`git commite`一次性提交所有修改


**丢弃工作区修改**
回到最近一次`commit/add`状态
```
git checkout -- [文件名]
```
当提交到暂存区后，撤销修改
```
git reset HEAD [文件名]
```

**删除文件**
从版本库删除文件
```
git rm [文件名]
git commit -m 'xxx'
```

删除错误，可以恢复到最近版本
```
git checkout -- [文件名]
```


## 远程仓库
**本地关联远程仓库**
```
git remote add origin git@xxxxx.com:xxx/xxx.git
```

**本地仓库推送**
将主分支的内容推送到远程仓库
```
git push -u origin master #推送+关联
git push origin master
```

**远程库克隆**
```
git clone git@xxxxx.com:xxx/xxx.git
```


## 分支管理
**创建并切换分支**
```
git checkout -b dev
```

**查看分支**
```
git branch
```

**合并分支**
```
git merge dev
```

**删除分支**
```
git branch -d dev
```

**switch**
也可以用switch，更符合
```
git switch -c dev
git switch master
```

**合并冲突**
使用支持3-way-merge的合并工具，`KDiff3`，`vimdiff`等

配置mergetool
```
git config --global --add merge.tool kdiff3
git config --global --add mergetool.kdiff3.path "C:/Program Files/KDiff3/kdiff3.exe"
git config --global --add mergetool.kdiff3.trustExitCode false

git config --global --add diff.guitool kdiff3
git config --global --add difftool.kdiff3.path "C:/Program Files/KDiff3/kdiff3.exe"
git config --global --add difftool.kdiff3.trustExitCode false
```

合并分支时
```
git merge dev
```
如果出现冲突，可以使用
```
git mergetool
```

**合并分支**
分支合并图
```
git log --graph
```

普通模式合并
```
git merge --no-ff -m "xxx" dev
```

## 多人协作
**查看远程库信息**
```
git remote
```

**创建远程分支到本地**
```
git checkout -b dev origin/dev
```

有冲突时，先拉取，再推送。拉取前需要先关联
```
git branch --set-upstream-to=origin/dev dev
git pull
git push origin dev
```

**Rebase**



## Merge Request工作流
1. 在git仓库上创建Issue，填写描述
2. 在Issue上创建对应的MR，命名方式`id-描述`(描述最好是英文，中午本地pull和push都麻烦)，目标分支指向dev
3. 从本地仓库获取远程新的分支
```
git fetch origin dev
git checkout -b [localdev] origin/dev
git pull origin dev
```
4. 本地切换到新分支，修改后推送到远程
5. 在MR页面添加描述`Edit`按钮
6. 在MR页面查看diff，添加`comment`
7. 合并后删除远程分支
