hadoop-1 192.168.137.110    
hadoop-2 192.168.137.120    
hadoop-3 192.168.137.130    

用户名：hadoop/123

JDK  
/usr/local/java/jdk1.8.0_161

## SSH免密登录
```
##所有机器执行以下命令
ssh-keygen
cd ~/.ssh/
ll --查看是否有 authorized_keys
rm -f authorized_keys ##有的话删除掉
hadoop@hadoop-2: 
  scp id_rsa.pub hadoop@192.168.137.110:~/.ssh/20_id_rsa.pub

hadoop@hadoop-3:  
  scp id_rsa.pub hadoop@192.168.137.110:~/.ssh/30_id_rsa.pub  

hadoop@hadoop-1:
  touch authorized_keys
  cat id_rsa.pub >> authorized_keys
  cat 20_id_rsa.pub >> authorized_keys
  cat 30_id_rsa.pub >> authorized_keys
  scp authorized_keys hadoop@192.168.137.120:~/.ssh/
  scp authorized_keys hadoop@192.168.137.130:~/.ssh/

hadoop@hadoop-2:
  ssh hadoop@192.16.137.110
  ##不用输入密码了
  
## 修改hostname 不用记很长的IP
--每台机器执行以下命令

sudo vim /etc/hosts

192.168.137.110 master
192.168.137.120 slave-2
192.168.137.130 slave-3

```


hadoop
/usr/local/hadoop/hadoop-2.6.5/
``` bash
sudo mkdir /usr/local/hadoop
sudo chmod -R /usr/local/hadoop/
cd /usr/local/hadoop/
curl -O http://mirror.rise.ph/apache/hadoop/common/hadoop-2.6.5/hadoop-2.6.5.tar.gz --下载 机器IP:192.168.137.110
##将文件复制到其他机器
scp hadoop-2.6.5.tar.gz hadoop@192.168.137.120:/usr/local/hadoop/
scp hadoop-2.6.5.tar.gz hadoop@192.168.137.130:/usr/local/hadoop/
## 解压
tar hadoop-2.6.5.tar.gz hadoop-2.6.5
cd hadoop-2.6.5 && mkdir ~/tmp

```

集群配置vim
```bash
hadoop@hadoop-1:
	cd /usr/local/hadoop/hadoop-2.6.5/etc/hadoop
	vim hadoop-env.sh
		export JAVA_HOME=/usr/local/java/jdk1.8.0_161
	vim slaves  --slave节点的IP或者域名
		slave-2
		slave-3
	vim core-site.xml
		<configuration>
			<property>
				<name>fs.defaultFS</name>
				<value>hdfs://master:9000</value>
			</property>
			<property>
				<name>hadoop.tmp.dir</name>
				<value>/home/hadoop/tmp</value>
			</property>
		</configuration>	
	vim hdfs-site.xml
		<configuration>
			<property>
				<name>dfs.replication</name>
				<value>2</value>
			</property>
		</configuration>
	vim mapred-site.xml
		<configuration>
			<property>
				<name>mapreduce.framework.name</name>
				<value>yarn</value>
			</property>
		</configuration>
	vim yarn-site.xml
		<configuration>
		<!-- Site specific YARN configuration properties -->
			<property>
				<name>yarn.resourcemanager.hostname</name>
				<value>master</value>
			</property>
			<property>
				<name>yarn.nodemanager.aux-services</name>
				<value>mapreduce_shuffle</value>
			</property>
		</configuration>	

	##将配置好的hadoop复制到其他服务器上
	cd ../../ 
	pwd
	/usr/local/hadoop/
	scp -r hadoop-2.6.5 hadoop@slave-2:/usr/local/hadoop/		
	scp -r hadoop-2.6.5 hadoop@slave-3:/usr/local/hadoop/
	
	cd /usr/local/hadoop/hadoop-2.6.5/sbin
	start-all.sh --观察启动是否成功
	stop-all.sh  --关闭集群
	
```



#### Hadoop 权威指南书本代码运行 -在linux系统中
```
git clone git@github.com:tomwhite/hadoop-book.git
cd hadoop-book
mvn clean package -DskipTests #安装好Maven

```















# Virtual Box 
想要的网络环境：
- ssh链接虚拟机，固定IP
- 虚拟机之间ping通，固定IP
- 虚拟机可以联网

实现方式-虚拟机开通2张网卡:
- host-only 可以实现主机和虚拟机的ping通，虚拟机不能上网
- 虚拟机使用桥接方式上网

## host-only 设置
- virtualbox 全局工具 -> 主机网络管理工具 -> 创建 VirtualBox Host-Only Ethernet Adapter
- 在主机的网络共享中心 将VirtualBox Host-Only Network 网络的IP设置为固定IP，如：192.168.137.1- 这样主机和虚拟机的联络就可以固定
- 将VirtualBox Host-Only Ethernet Adapter的IP设置: 手动配置网卡 -> IP4地址:`主机中的Host-Only中的IP地址，如：192.168.137.1` -> 网络掩码:255.255.255.0
- 进入虚拟机将虚拟机的 IP地址设置为:192.168.137.1 同一网段，如：192.168.137.110 网关：192.168.137.1 掩码:255.255.255.0

经过这样的配置后:主机 ping:192.168.137.110 虚拟机ping:192.168.137.1 都是通的
     
## 虚拟机的连通网络 - 桥接
- 增加一个网络连接方式- 桥接
- 在虚拟机中(ubuntu-server 16.04)`/etc/network/interfaces` 添加网络 整个配置过程如下：
```
$ sudo vim /etc/network/interfaces

# This file describes the network interfaces available on your system
# and how to activate them. For more information, see interfaces(5).

source /etc/network/interfaces.d/*

# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
auto enp0s8 # 上外网的网卡名称
iface enp0s8 inet dhcp

# this Host-Only Network
auto enp0s3
iface enp0s3 inet static
address 192.168.137.120
netmask 255.255.255.0
gateway 192.168.137.1

```

这样就可以上外网也能和主机保持一个局域网的环境















	 



