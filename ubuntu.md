# Ubuntu
* 删除软件
    1. `sudo apt-get remove --purge Sublime Text` 或者 `sudo apt-get autoremove --purge softName`
    2. `dpkg --get-selections|grep 'softName'`,`sudo apt-get purge `一个带core的package，如果没有带core的package，则是情况而定。

*   清除数据   
    `dpkg -l |grep ^rc|awk '{print $2}' |sudo xargs dpkg -P `

*   日志查看命令：   
    `cat -n test.log |grep "地形"`  ---查看有包含'地形'的日志
