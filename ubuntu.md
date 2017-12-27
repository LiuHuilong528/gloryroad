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

*   ` adduser ` 命令会创建用户名、用户组、`home` 下的目录，可以使用 **用户登陆系统**；
    `userdel -r username` 删除用户名和用户目录

*   ` useradd ` 仅仅添加用户， **不会有目录添加**

*   ` dhclient ` - 动态配置网络

*   忘记登陆密码          
  1. 进入 `recovery mode`
  2. 选择 `root drop to root shell prompt` ，按 `enter`
  3. ` passwd username ` , 输入新密码
  4. 出现 ` authentication token manipulation error passwd unchanged ` ,则运行 ` mount -o rw,remount / ` 后修改密码

*   修改目录下的权限: `chmod -R 700 Document/`

*   apt-get 国内源，` vi /etc/apt/sources.list `         
    ```txt
        deb http://mirrors.aliyun.com/ubuntu/ vivid main restricted universe multiverse
        deb http://mirrors.aliyun.com/ubuntu/ vivid-security main restricted universe multiverse
        deb http://mirrors.aliyun.com/ubuntu/ vivid-updates main restricted universe multiverse
        deb http://mirrors.aliyun.com/ubuntu/ vivid-proposed main restricted universe multiverse
        deb http://mirrors.aliyun.com/ubuntu/ vivid-backports main restricted universe multiverse
        deb-src http://mirrors.aliyun.com/ubuntu/ vivid main restricted universe multiverse
        deb-src http://mirrors.aliyun.com/ubuntu/ vivid-security main restricted universe multiverse
        deb-src http://mirrors.aliyun.com/ubuntu/ vivid-updates main restricted universe multiverse
        deb-src http://mirrors.aliyun.com/ubuntu/ vivid-proposed main restricted universe multiverse
        deb-src http://mirrors.aliyun.com/ubuntu/ vivid-backports main restricted universe multiverse
    ```

*   修改hosts         
    ```shell
      $ sudo vi /etc/hosts
      ## 最好是将 hostname 统一绑定到 127.0.0.1 上
      $ sudo /etc/init.d/networking restart
    ```

*   openssh-server 安装
    ```shell
        sudo apt-get install  openssh-server
        # 查看是否启动
        ps -e | grep ssh 或者 netstat -antp|grep 22

        # 重启sshserver
        sudo /etc/init.d/ssh restart
    ```
*   添加开机脚本      
    1. 新建脚本 `newservice.sh`
    2. 设置权限 ` sudo chmod 788 newservice.sh `
    3. 将脚本移动到启动目录下 ` sudo mv newservice.sh /etc/init.d/ `
    4. 将脚本添加到启动脚本中，90 为优先级，越高越晚执行：      
        ```
          cd /etc/init.d/
          sudo update-rc.d newservice.sh defaults 90

          ## 移除开机脚本
          sudo update-rc.d -f newservice.sh remove
        ```
*    新增 shh 连接用户，以 root 用户为例：     
        1. 修改 /etc/ssh/sshd_config        
            ```shell
                sudo vi /etc/ssh/sshd_config
            ```
        2. 找到 ` #PermitRootLogin without-password `
            ```
              PermitRootLogin yes
            ```
        3. 重启 ` ssh ` 服务        
            ```
                sudo service ssh restart
            ```

*  修改系统时区      
    ```shell
        # 查看时区
        date -R
        # 修改当前用户时区
        sudo tzselect
        # 修改系统时区
        sudo cp /usr/share/zoneinfo/Asia/Shanghai  /etc/localtime
        # 更新时间
        sudo ntpdate time.windows.com
    ```
*   登录时，默认的 `shell` 不是 ` bash `     
    ```shell
      # 查看机器安装的shell
      chsh -l 或者 cat /etc/shells
      # 查看当前使用shell
      echo $SHELL --大写
      # 修改默认sehll
      chsh -s /bin/bash
    ```
*   不同机器之间拷贝文件    
    ```shell
      scp [user@host:]file destination  
    ```
* LVM 调整分区大小          
  >\# vgdisplay       
  \# lvdisplay lhl-vg     
    ``````
      --- Logical volume ---
      LV Path                /dev/lhl-vg/swap_1   
      LV Name                swap_1   
      VG Name                lhl-vg   
      LV UUID                aZRGkt-88Mw-aV1V-8t2X-Mn6F-tRmF-y9XXSj   
      LV Write Access        read/write   
      LV Creation host, time lhl, 2017-11-08 15:10:49 +0800   
      LV Status              available    
      # open                 2   
      LV Size                4.00 GiB   
      Current LE             1023   
      Segments               1    
      Allocation             inherit    
      Read ahead sectors     auto   
      - currently set to     256    
      Block device           252:1    
      ·······
    ``````   
  \# lvreduce -L -1G -f /dev/lhl-vg/swap_1    
  \# lvcreate -L 1G -n mylvm /dev/lhl-vg      
  \# mkdir /mylvm       
  \# mke2fs -j -t ext4 /dev/lhl-vg/mylvm      
  \# mount -t ext4 /dev/lhl-vg/mylvm /mylvm       

* 桌面快捷方式配置——IDEA的桌面快捷方式      
  ```
  [Desktop Entry]
  Encoding=UTF-8
  Name=IdeaIU
  Comment=IdeaIU
  Exec=/home/carl/soft/idea-IU-172.4343.14/bin/idea.sh
  Icon=/home/carl/soft/idea-IU-172.4343.14/bin/idea.png
  Terminal=false
  Type=Application
  Categories=Application;Development;        
  ```

查看端口情况：
>
netstat -ap | grep port


# Centos 7

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

- 修改密码     
  ```bash
  passwd userName
  # 接下来输入新密码

  ```


### Vim
* ` set number ` - 显示行号



### 网络设置      
1. 查看 ` nmcli connection show `
2. 设置        
    ```shell
    vi /etc/sysconfig/network-scripts/ifcfg-enp0s3  #ifcfg-enp0s3 网卡名

    BOOTPROTO="static" #dhcp改为static   
    ONBOOT="yes" #开机启用本配置  
    IPADDR=192.168.7.106 #静态IP  
    GATEWAY=192.168.7.1 #默认网关  
    NETMASK=255.255.255.0 #子网掩码  
    DNS1=8.8.8.8 #DNS 配置

    service network restart  # 重启网络服务
    ######################################
    ##    另外的方式                     ##
    ######################################
    nmcli connection modify
    ```
- 列出所有版本信息         
  ```bash
    lsb_release -a
  ```

- 关闭 X 桌面     
  ```bash
    init 3
  ```
- 软连接建立删除           
  ```shell
      rm -rf symbolic_name
      $xz -d ***.tar.xz

      $tar -xvf  ***.tar
  ```

- 查看显卡信息       
  ```shell
      lspci -vnn | grep VGA -A 12
      rpm -qa | grep glibc # 查看GLIBC
  ```















# c
