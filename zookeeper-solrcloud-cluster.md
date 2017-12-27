# Zookeeper 集群搭建
>cd Zookeeper     
cp conf/zoo_sample.cfg conf/zoo.cfg     
vi conf/zoo.cfg     
  ```
  tickTime=2000
  dataDir=/var/lib/zookeeper/
  clientPort=2181
  initLimit=5
  syncLimit=2
  server.1=192.168.1.25:2888:3888
  server.2=192.168.1.55:2888:3888
  server.3=192.168.1.112:2888:3888  
  ```     
 #以下为日志文件位置配置  
vi bin/zkEnv.sh     
  ```
  ZOO_LOG_DIR="../logs" # 在当前目录创建logs 目录
  ...
  ZOO_LOG4J_PROP="INFO,ROLLINGFILE"
  ```   
vi conf/log4j.properties    
  ```
  zookeeper.root.logger=INFO, ROLLINGFILE
  ```   
  #将zookeeper 服务器的id 写入 myid 文件    
  echo "1" > /home/datatouch/data/zookeeper/myid    
  cd bin      
  ./zkServer.sh start  ## 启动zookeeper



# SolrCloud 搭建
>     
  cd {$SOLR_HOME}/server/scripts/cloud-scripts    
   ./zkcli.sh -zkhost zk:2183 -cmd upconfig -confdir {SOLR_HOME}/solrhome1/collection1/conf -confname myconf # 上传配置文件     
   vi ${SOLR_HOME}/solrhome1/collcollection1/solr.xml #修改solr的ip和端口号     
   ```
   <str name="host">${host:}</str>
   <int name="hostPort">${jetty.port:8983}</int>   
   ```      
   vi catalina.sh # 使用了Tomcat
   ```
   JAVA_OPTS="-DzkHost=zkHost"
   ```      
   cd {TOMCAT_HOME}/bin   
   ./start.sh  # 启动tomcat 即可到端口访问solr界面


----  
Solr 扩展
1. 增加Shard    
  只有 `router` 为 implicit 的collection才可以动态添加shard    
  ```
  /admin/collections?action=CREATESHARD&shard=2014&collection=AuditTrailIndex
  ```
2. 增加机器-ADDREPLICA
  增加副本集，不能超过 `maxShardsPerNode` 的配置值    
  在现有的集群中新启动一个Solr实例，调用以下接口
  ```
  /admin/collections?action=ADDREPLICA&collection=collection&shard=shard&node=nodeName      
  ```
  在UI界面上新增 Replica 就可以了
































****
