# Git

## 本地仓库

### 创建本地仓库

```shell
git init        #任意文件夹
git status      #状态
```

### 添加、提交

```shell
git add 文件名                        #追踪文件    （add .）追踪所有文件
git rm --cached 文件名                #取消跟踪
git rm 文件名                         #删未提交的文件(暂存区+当前文件夹)
git commit -m '版本名'                #提交
git log --oneline --graph --all      #显示提交日志
git show 版本号                       #修改变化
```

- `.gitignore`文件内的文件名为被忽略文件
- `git commit -a -m '版本名'`已追踪的文件不需`git add`

### 回滚

```shell
git reset --hard 版本号                #使工作空间返回
git reset HEAD 文件名                  #取消文件暂存(重复执行:依次取消历史提交!!)
git reset HEAD~                       #返回上一次提交 
git restore 文件名                     #从暂存区恢复文件
git restore --staged 文件名            #将提交区HEAD指向的版本恢复到暂存区
git reflog                            #显示所有历史日志
```

### 分支

```shell
git branch                             #查看当前存在分支（*为所在分支）
git branch 分支名                       #创建新分支
git branch -d 分支名                    #删除分支
git checkout 分支名                     #切换分支
git switch 分支名                       #切换分支
git merge 分支名                        #合并分支内容（不是合并分支）
git diff                               #查看冲突
git diff 版本号 文件名(可选)              #查看与过去版本的差异
git rebase 分支名                       #将当前分支变基到指定分支
git cherry-pick 版本号                  #将其它修改拿到当前分支（优选）
```

- 解决冲突后再合并
- 创建分支就是创建了一个指向当前对象的指针

## 远程仓库

### push

```shell
git remote add 远程仓库名称 地址（HTTP）             #添加远程仓库
git remote                          			  #查看远程仓库名称
git push -u 远程仓库名 本地分支名                    #第一次推送
git push 远程仓库名称 本地分支名称[:远端分支名称]       #推送本地分支
git push -f                                       #强制推送并抹去上次提交!!

#远端与本地分支绑定
git push --set-upstream 远程仓库名称 master:master  #绑定
git push 远程仓库名称                               #同步分支
```

### clone

```shell
git clone 地址（HTTP）                              #下载远程仓库
```

### 冲突

- 他人在本机提交之前push了新内容（本机与远程仓库不一致）

```shell 
git fetch 远程仓库名称                                #同步最新远程仓库
git merge 分支名                                     #合并
git push 远程仓库名称                                 #更新、同步
```

- 文件内容出现不一致

```shell
git fetch                                           #抓取：只获取，不合并
git pull                                            #拉取：获取+合并
git push                                            #更新
```

- 文件版本的历史不同

```shell
git pull origin main --allow-unrelated-histories
```

## 代理

```shell
$ git config --global --unset http.proxy
#禁用代理
```

