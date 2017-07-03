#Git
##git 链接 github 步骤：

    1.创建 SSH Key: `ssh-keygen -t rsa -C "email"` 生成公私密钥
    2.在github上添加公钥，方便私钥验证；`cat ~/.ssh/id_rsa.pub` 查看密钥
    3.ssh 连接 github ：`ssh -T git@github.com`


**git 常见命令:**

*   `git clone url`  - 从github下载代码到本地
*   `git status`  -现在git状态:新增、修改、删除等
*  `git add filename` -将文件添加如git 版本管理中(暂存区).
    _注意_：此命令并不提交代码到仓库，只是将之前在版本管理之外的文件，纳入版本管理之中
*   `git commit -m "comment"` -提交（暂存区）代码至(本地)仓库
*   `git log` -查看git提交日志
*   `git push`  - 更新 GitHub上仓库 
*   `git init ` - 本地初始化仓库
*   `git log --pretty=short` -美化输出格式
*   `git log -p <fileName>`  - 提交前后差别
*   `git diff` --查看工作树、暂存区、最新提交之间的差别
    > `git diff` 工作树和暂存区差别
    
    > `git diff HEAD` 工作树和最新提交差别
 
*   `git branch` -显示分支
*  创建、切换分支
    > `git checkout -b branchname` --创建分支 并切换到创建的分支下

    > `git branch branchname`  `git checkout branchname` 同上一样的效果
    
*   `git merge --no-ff branchA` --合并分支;将branchA合并到

