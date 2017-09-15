# Ubuntu
* 删除软件
    1. `sudo apt-get remove --purge Sublime Text` 或者 `sudo apt-get autoremove --purge softName`
    2. `dpkg --get-selections|grep 'softName'`,`sudo apt-get purge `一个带core的package，如果没有带core的package，则是情况而定。

*   清除数据   
    `dpkg -l |grep ^rc|awk '{print $2}' |sudo xargs dpkg -P `

*   日志查看命令：   
    `cat -n test.log |grep "地形"`  ---查看有包含'地形'的日志
*   安装下载的 ` .dep ` 文件：
    > sudo dpkg -i <file>.deb


# Centos

- 添加用户    
  ```bash
    useradd userName
    passwd userName passwd
  ```
- 添加 sudo 权限    
  ```bash
    chmod -v u+w /etc/sudoers  -- 修改文件写的权限
    vi /etc/sudoers
    # 添加  dev  ALL=(ALL) ALL
    chmod -v u-w /etc/sudoers  --改为只读
  ```
