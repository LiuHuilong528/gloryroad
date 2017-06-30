#Git
##git 链接 github 步骤：

    1.创建 SSH Key: `ssh-keygen -t rsa -C "email"` 生成公私密钥
    2.在github上添加公钥，方便私钥验证；`cat ~/.ssh/id_rsa.pub` 查看密钥
    3.ssh 连接 github ：`ssh -T git@github.com`


##git 常见命令:

*   `git clone url`  - 从github下载代码到本地
*   `git status`  -现在git状态:新增、修改、删除等
*  `git add filename` -将文件添加如git 版本管理中(暂存区).
    _注意_：此命令并不提交代码到仓库，只是将之前在版本管理之外的文件，纳入版本管理之中
*   `git commit -m "comment"` -提交（暂存区）代码至仓库
*   `git log` -查看git提交日志
*  `git push`  - 更新 GitHub上仓库 
*  `git init ` - 本地初始化仓库
