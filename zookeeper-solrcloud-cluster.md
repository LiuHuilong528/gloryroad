# Zookeeper 集群搭建
>cd Zookeeper  # 相当于进入 ${ZK_HOME} 目录   
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
  ZOO_LOG_DIR="../logs" # 在${ZK_HOME} 目录创建logs文件夹
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

# Solr zookeeper 配置文件 —— 利用了 `solr.sh`
上传配置文件到zookeeper中       
`bin/solr zk upconfig -z zkHost:port -n mynewconfig -d /path/to/upload`

下载zookeeper中的配置文件到本地        
`bin/solr zk downconfig -z zkHost:port -n downloadedConf -d /local/path`

本地文件复制到zookeeper中     
1. 文件夹：`bin/solr zk cp -r file:/apache/confgs/whatever/conf zk:/configs/myconf -z zkHost:port`
2. 单个文件：`bin/solr zk cp zk:/configs/myconf/managed_schema /configs/myconf/managed_schema -z zkHost:port`


## solr FastVectorHighlight -多种背景色高亮 -- 6.3
managet-schema 修改：      
``` xml
.......
<field name="text_textansj" type="text_ansj" indexed="true" termOffsets="true" stored="true" termPositions="true" termVectors="true"/>
.......
```

查询语句：
``` java
 SolrQuery solrQuery = new SolrQuery();
 solrQuery.setFacet(false);
 solrQuery.add("indent","on");
 solrQuery.add("df", "text_textansj");
 solrQuery.add("hl.fl" , "text_textansj");
 solrQuery.add("hl", "on");
 solrQuery.add("q","取消订单  or 优惠券");//查询条件
 solrQuery.add("hl.method" , "fastVector");
 solrQuery.add("hl.fragListBuilder" , "weighted");
 solrQuery.add("hl.fragmentsBuilder" , "colored");
 solrQuery.add("hl.q" , "取消订单  or 优惠券");//需要高亮的内容
 solrQuery.add("fl","id");
 solrQuery.add("hl.useFastVectorHighlighter" , "true");//不能少
 solrQuery.add("hl.fragsize", "10000");
 solrQuery.add("wt" , "json");
```

----  
# Solr 扩展
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
  在UI界面上新增 Replica 即可！



### Solr 脚本操作
#### solr 重启：		
> ./bin/solr start -c -p 8983 -s example/cloud/nodel1/solr        
./bin/solr statr -c -p 7574 -s example/cloud/nodel2/solr -z localhost:9983

#### solr 启动，关闭，新建Collection，delete Collections 等
>   
  bin/solr start [-f] [-c] [-h hostname] [-p port] [-d directory] [-z zkHost] [-m memory] [-e example] [-s solr.solr.home 实例目录] [-t solr.data.home 数据] [-a "additional-options"] [-V] # 启动     
>
  bin/solr stop [-k key] [-p port] #关闭solr实例      

>  bin/solr create [-c name] [-d confdir] [-n confname] [-shards #] [-replicationFactor #] [-p port] [-V] #创建新集合   

>  bin/solr delete [-c name] [-deleteConfig boolean] [-p port] [-V]  #删除集合       

> bin/post -c techproducts example/exampledocs/*  # 导入数据    

#### 启动已经运行过的Solr实例
> ./bin/solr start -c -p 8983 -s example/cloud/node1/solr #启动第一个       
> ./bin/solr start -c -p 7574 -s example/cloud/node2/solr -z localhost:9983 #启动第二个节点


#### Solr `catchall` CopyField
Add Copy Filed -> Source:* -> destination:_text_ 这个操作会将所有字段的值copy到名为 `_text_` 的字段上，此字段会建索引和存储；意味为所有的索引会建2次。

#### solr 创建Collection  
> bin/solr create -c films -s 2 -rf 2


#### 创建 Solr 数据索引		
> bin/solr -c films example/films/films.json


#### Range Facets     		
> curl 'http://localhost:8983/solr/films/select?q=*:*&rows=0'\      
 '&facet=true'\        
 '&facet.range=initial_release_date'\            
 '&facet.range.start=NOW-20YEAR'\            
 '&facet.range.end=NOW'\         
 '&facet.range.gap=%2B1YEAR'   


#### Pivot Facets-决策树Facet查询		
> curl "http://localhost:8983/solr/films/select?q=*:*&rows=0&facet=on&facet.pivot=genre_str,directed_by_str"

#### 删除 Collection			
> bin/solr delete -c films


#### 创建索引的方法：
* `bin/post` 利用 Post Tool，支持 JSON、XML、CSV、HTML、PDF、MSWord等
* `DataImportHandler` 能连接到数据库、邮件服务器或其他结构化数据源
* `SolrJ` solr与java语言交流工具
* `Documents Screen` -Solr 的界面管理工具

#### 删除数据		
> bin/post -c localDocs -d "<delete><id>123</id></delete>"			
bin/post -c localDocs -d "<delete><query>*:*</query></delete>"

### 将Solr安装为机器的服务运用系统命令管理

1. 从 `.tgz` 中抽取出脚本		
> tar xzf solr-7.1.0.tgz solr-7.1.0/bin/install_solr_service.sh --strip-components=2    

2. 安装为服务的命令		
> sudo bash ./install_solr_service.sh solr-7.1.0.tgz -i /opt -d /var/solr -u solr -s solr -p 8983

3. Solr Home Directory		
>  `/var/solr/data` ## 可以用参数 -d 进行自定义

4. 环境变量覆盖		
>		
SOLR_PID_DIR=/var/solr    
SOLR_HOME=/var/solr/data    
LOG4J_PROPS=/var/solr/log4j.properties    
SOLR_LOGS_DIR=/var/solr/logs    
SOLR_INSTALL_DIR=/opt/solr    
SOLR_ENV=/etc/default/solr.in.sh    
RUNAS=solr    

在 `/etc/default/solr.in.sh` 修改 JVM 运行参数


















---

# Solr in Action
## Solr Features overview

### 用户体验
1. Pagination and sorting
2. Faceting
3. Autosuggest
4. Spell-checking
5. Hit highlighting
6. Geospatial search

### 数据模型支持
1. Result grouping/field collapsing
2. Flexible query support
3. Joins
4. Document clustering
5. Importing rich document formats such as PDF and Word
6. Importing data from relational databases
7. Multilingual support

### 新特性
1. Near real-time search
2. Atomic updates with optimistic concurrency
3. Real-time get
4. Write durability using a transaction log
5. Easy sharding and replication using ZooKeeper
























****
