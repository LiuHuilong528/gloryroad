# 由低版本的Spring-boot 升级到新版本

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-properties-migrator</artifactId>
  <scope>runtime</scope>
</dependency>
```

`spring-boot-properties-migrator` 这个工具能够帮助，分析程序的环境变量，也能暂时性的在运行时移植属性；

**注意:** `@PropertySource` 的属性是不会被纳入运行时替换的考虑中；一旦升级完成，则应将以上依赖删除；

# 11. 第一Spring-Boot 应用

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
<modelVersion>4.0.0</modelVersion>
<groupId>com.example</groupId>
<artifactId>myproject</artifactId>
<version>0.0.1-SNAPSHOT</version>
<parent>
 <groupId>org.springframework.boot</groupId>
 <artifactId>spring-boot-starter-parent</artifactId>
 <version>2.1.0.BUILD-SNAPSHOT</version>
</parent>
<!-- Additional lines to be added here... -->
<!-- (you don't need this if you are using a .RELEASE version) -->
<repositories>
 <repository>
 <id>spring-snapshots</id>
 <url>https://repo.spring.io/snapshot</url>
  <snapshots><enabled>true</enabled></snapshots>
 </repository>
 <repository>
 <id>spring-milestones</id>
 <url>https://repo.spring.io/milestone</url>
 </repository>
</repositories>
<pluginRepositories>
 <pluginRepository>
 <id>spring-snapshots</id>
 <url>https://repo.spring.io/snapshot</url>
 </pluginRepository>
 <pluginRepository>
 <id>spring-milestones</id>
 <url>https://repo.spring.io/milestone</url>
 </pluginRepository>
</pluginRepositories>
</project>
```






