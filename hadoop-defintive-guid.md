# Hadoop Defintive Guid

## Meet Hadoop

### Hadoop 和 RDBMS
- Hadoop适用大量数据：一次写多次读；RDBMS适用小量数据：查询和多次更新
- Hadoop适用半结构或非结构化数据；RDBMS适用结构化数据
- Hadoop可以线性扩展；RDBMS不能够

### Hadoop 和 Grid Computing
- Grid Computing 适合计算密集的工作，数据量大了网络成为了短板；
- Grid Computing 需要程序代码处理失败重试，而Hadoop的MapReduce本身就帮助处理了此类问题


### Volunteer Computing 和 Hadoop

## MapReduce
书本的代码运行需要搭建好单节点和maven

```
git clone git@github.com:tomwhite/hadoop-book.git
cd hadoop-book
mvn clean package -DskipTests #安装好Maven
export HADOOP_CLASSPATH=hadoop-example.jar
hadoop MaxTemperature input/ncdc/sample.txt output
```

**书中代码在单节点中运行时，需要向上面一样设置`HADOOP_CLASSPATH`**

- MapReduce的Split 大小和Block 大小一样
- MapReduce会将结果输出到本地硬盘而不是HDFS
























