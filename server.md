# 服务器相关

***

## MongoDB
### 安装

#### 包

安装包包含的内容：

```shell
|--MongoDB-org
      |--mongodb-org-server
      |--mongodb-org-mongos
      |--mongodb-org-shell
      |--mongodb-org-tools
```

- MongoDB-org 元数据包，会自动安装此目录下的四个包
- mongodb-org-server 包含 ` mongod ` 的守护线程、相关配置和初始化脚本
- mongodb-org-mongos 包含 ` mongos ` 的守护线程
- mongodb-org-shell 包含 ` mongo ` 脚本语言
- mongodb-org-tools 包含 ` mongooplog ` `mongoperf ` ` mongorestore ` ` mongostat ` ` mongotop ` 等工具。

### 配置文件

样例：

>  
```yml
systemLog:    
   destination: file    
   path: "/var/log/mongodb/mongod.log"    
   logAppend: true    
storage:    
   journal:    
      enabled: true    
processManagement:    
   fork: true    
net:    
   bindIp: 127.0.0.1    
   port: 27017    
setParameter:    
   enableLocalhostAuthBypass: false
···   
```

配置文件的使用： ` mongod --config configFile ` 或者 ` mongod -f configFile `

#### 核心配置项

##### **systemLog** Options

>   
```yml
systemLog:
   verbosity: <int>
   quiet: <boolean>
   traceAllExceptions: <boolean>
   syslogFacility: <string>
   path: <string>
   logAppend: <boolean>
   logRotate: <string>
   destination: <string>
   timeStampFormat: <string>
   component:
      accessControl:
         verbosity: <int>
      command:
         verbosity: <int>
      # COMMENT additional component verbosity settings omitted for brevity
      storage:
        verbosity:<int>
        journal:
          verbosity:<int>
      write:
          verbosity:<int>
```

#### **processManagement** Options

>  
```yml
processManagement:
   fork: <boolean>
   pidFilePath: <string>
```

#### **net** Options

>   
```yml
net:
   port: <int>
   bindIp: <string>
   maxIncomingConnections: <int>
   wireObjectCheck: <boolean>
   ipv6: <boolean>
   unixDomainSocket:
      enabled: <boolean>
      pathPrefix: <string>
      filePermissions: <int>
   http:
      enabled: <boolean>
      JSONPEnabled: <boolean>
      RESTInterfaceEnabled: <boolean>
   ssl:
      sslOnNormalPorts: <boolean>  # deprecated since 2.6
      mode: <string>
      PEMKeyFile: <string>
      PEMKeyPassword: <string>
      clusterFile: <string>
      clusterPassword: <string>
      CAFile: <string>
      CRLFile: <string>
      allowConnectionsWithoutCertificates: <boolean>
      allowInvalidCertificates: <boolean>
      allowInvalidHostnames: <boolean>
      disabledProtocols: <string>
      FIPSMode: <boolean>
   compression:
      compressors: <string>
```


#### **security** Options

>   
```yml
security:
   keyFile: <string>
   clusterAuthMode: <string>
   authorization: <string>
   transitionToAuth: <boolean>
   javascriptEnabled:  <boolean>
   redactClientLogData: <boolean>
   sasl:
      hostName: <string>
      serviceName: <string>
      saslauthdSocketPath: <string>
   enableEncryption: <boolean>
   encryptionCipherMode: <string>
   encryptionKeyFile: <string>
   kmip:
      keyIdentifier: <string>
      rotateMasterKey: <boolean>
      serverName: <string>
      port: <string>
      clientCertificateFile: <string>
      clientCertificatePassword: <string>
      serverCAFile: <string>
   ldap:
      servers: <string>
      bind:
         method: <string>
         saslMechanism: <string>
         queryUser: <string>
         queryPassword: <string>
         useOSDefaults: <boolean>
      transportSecurity: <string>
      timeoutMS: <int>
      userToDNMapping: <string>
      authz:
         queryTemplate: <string>
```

#### **storage.wiredTiger** Options

>   
```yml
storage:
   wiredTiger:
      engineConfig:
         cacheSizeGB: <number>
         journalCompressor: <string>
         directoryForIndexes: <boolean>
      collectionConfig:
         blockCompressor: <string>
      indexConfig:
         prefixCompression: <boolean>
```

#### **storage.inMemory** Options

>   
```yml
storage:
   inMemory:
      engineConfig:
         inMemorySizeGB: <number>
```

#### **operationProfiling** Options
>   
``` yml
operationProfiling:
   slowOpThresholdMs: <int>
   mode: <string>
```


#### **replication** Options

>    
```yml
replication:
   oplogSizeMB: <int>
   replSetName: <string>
   secondaryIndexPrefetch: <string>
   enableMajorityReadConcern: <boolean>
```

#### **sharding** Options
>   
```yml
sharding:
   clusterRole: <string>
   archiveMovedChunks: <boolean>
```


#### **auditLog** Options

>    
```yml
auditLog:
   destination: <string>
   format: <string>
   path: <string>
   filter: <string>
```


#### **snmp** Options

>    
```yml
snmp:
   subagent: <boolean>
   master: <boolean>
```

#### **Text Search** Options

>    
```yml
basisTech:
  rootDirectory: <string>
```

#### **mongos-only** Options
>   
```yml
replication:
   localPingThresholdMs: <int>

sharding:
   configDB: <string>
```

***
## Solr + Zookeeper = Solr-Cloud

Solr Cloud 特性：
- 整个集群的配置中心
- Automatic load balancing and fail-over for queries
- zookeeper 的集群协调和配置的集成

步骤：
1. 下载 ` zookeeper `     
2. 在 zk-dir 目录下解压下载的 ` zookeeper-3.3.6.tar.gz `
3. 




























# Solr + Zookeeper = Solr-Cloud
