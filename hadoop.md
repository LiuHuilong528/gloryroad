# Hadoop

## 搭建环境

``` shell
tar -zxvf hadoop-2.9.0.tar.gz -C hadoop   
cd hadoop && vi etc/hadoop/hadoop-env.sh
##　添加内容
  export JAVA_HOME={JAVA_HOME}
## 运行以下命令观察输出
bin/hadoop
```

这是伪集群和集群准备工作。

### 单机
以上过程完成了单机的hadoop环境搭建，可以进行Hadoop任务。
```bash
mkdir input
cp etc/hadoop/*.xml input
bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-3.0.0.jar grep input output 'dfs[a-z.]+'
cat output/*
```

### 伪集群 










----hadoop
