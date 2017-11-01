# Git

## git 链接 github 步骤：

    1.创建 SSH Key: ` ssh-keygen -t rsa -C "email" ` 生成公私密钥
    2.在github上添加公钥，方便私钥验证；`cat ~/.ssh/id_rsa.pub` 查看密钥
    3.ssh 连接 github ：`ssh -T git@github.com`


**git 常见命令:**

*   `git clone url`  - 从github下载代码到本地
*   `git status`  -现在git状态:新增、修改、删除等
*   `git add filename` -将文件添加如git 版本管理中(暂存区)。   
_注意_:此命令并不提交代码到仓库，只是将之前在版本管理之外的文件，纳入版本管理之中


*   `git commit -m "comment"` -提交（暂存区）代码至(本地)仓库
*   `git log` -查看git提交日志
*   `git push`  - 更新 GitHub上仓库
*   `git init ` - 本地初始化仓库
*   `git log --pretty=short` -美化输出格式
*   `git log -p <fileName>`  - 提交前后差别
*   `git reflog ` --查看当前仓库操作日志
*   `git diff` --查看工作树、暂存区、最新提交之间的差别
    > `git diff` 工作树和暂存区差别

    > `git diff HEAD` 工作树和最新提交差别

*   `git branch` -显示分支
*   创建、切换分支
    > `git checkout -b branchname` --创建分支 并切换到创建的分支下

    > `git branch branchname`  ` git checkout branchname `  同上一样的效果

*   `git merge --no-ff branchA` --合并分支;将branchA合并到主干

*   `git log --graph` -- 以图表形式查看分支
*   `git reset --hard hashvalue`  -- 回溯历史版本
*   `git commit --amend`  -- 修改提交信息记录
*   `git commit -am ` --先add 后commit 2步
*   `git rebase -i  HEAD-2` -- 合并提交历史，实现完美提交

    > pick 7a34294 Add feature-C

    > pick 6fba227 Fix typo

    修改成如下，则 `6fba227` 部分的提交会被合并到 前一个提交；

    > pick 7a34294 Add feature-C

    > fixup 6fba227 Fix typ


## git 推送至远程仓库

*   `git remote add  origin git@github.com:github-name/git-tutorial.git` -- 将github上的项目设置为本地库的远程库。

*   `git push` --推送至远程仓库
*   `git push -u origin master` -- `-u`参数将远程仓库的master分支设置为本地仓库的上游，方便以后从 master 分支获取内容；


## git 从远程仓库获取内容

*   `git clone ` -- 获取主干
*   `git checkout -b branchname romotebranchname` -- 以远程的分支为基础创建新的本地分支
*   `git pull`-- git 最新内容





## 消除 提交代码总是输入用户和密码,换种协议：不用 https 使用 ssh

*   `git remote rm origin ` -- 删除远程项目的别称“origin”
*   `git remote add origin git@github.com:githubAccount/Repository.git` --将仓库的协议更改为ssh，不是`https://github.com/githubAccount/Repository.git`(这是https协议的)
*   `git push -u origin master` --向远程仓库提交代码，不用密码了


## 删除远程仓库文件
```bash
  git rm -- cached "路径+文件名"
  git commit -m "delete file"  
  git pushf
```



## IDEA 快捷键
- ` ctrl + N ` - Open Type
- ` Ctrl + Alt + B ` - OpenImplementsMethods
- ` Ctrl + Alt + 空格 ` - 查看方法注释































------
