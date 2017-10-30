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
















#
