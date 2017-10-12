# Java
* ajax 传数组到后台：

``` javascript
var arr=[];
arr.push({"nam":"888"});
arr.push({"nam":"888"});
arr.push({"nam":"888"});
$.ajax(
    url,
    arr,//这里就是传给后台的数据——直接是数组；
    success,
    'json'
);

```

java后台：

``` java
//struts中
private List<String> name = new ArrayList<String>();
//这样就可以接到数据了
```

* ` Comparator `  中 ` a.compareTo(b) ` :    
  `1`  -> b a   
  `0`  -> a b   
  `-1` -> a b


### java8 时间和日期-API
* `ZoneId` ： 时区ID，用来确定Instant和LocalDateTime互相转换的规则
* `Instant` :  用来表示时间线上的一个点
* `LocalDate` 无时区日期，不可变线程安全
* `LocalTime` 无时区时间，不可变线程安全
* `LocalDateTime` 无时区时间戳，不可变线程安全
* `Clock` : 用于访问当前时刻、日期、时间，用到时区
* `Duration` : 用秒和纳秒表示时间的数量


# JQuery
- 可视化过滤选择器：    
  - `$("div:hidden")`        选择所有的被hidden的div元素    
  - `$("div:visible")`        选择所有的可视化的div元素


- 属性过滤选择器：
  - `$("div[id]")`              选择所有含有id属性的div元素
  - `$("input[name='newsletter']")`    选择所有的name属性等于'newsletter'的input元素
  - `$("input[name!='newsletter']")` 选择所有的name属性不等于'newsletter'的input元素
  - `$("input[name^='news']")`         选择所有的name属性以'news'开头的input元素
  - `$("input[name$='news']")`         选择所有的name属性以'news'结尾的input元素
  - `$("input[name*='man']")`          选择所有的name属性包含'news'的input元素
  - `$("input[id][name$='man']")`    可以使用多个属性进行联合选择，该选择器是得到所有的含有id属性并且那么属性以man结尾的元素


- 子元素过滤选择器：
  - `$("ul li:nth-child(2)")`,`$("ul li:nth-child(odd)")`,`$("ul li:nth-child(3n + 1)")`
  - `$("div span:first-child")`          返回所有的div元素的第一个子节点的数组
  - `$("div span:last-child")`           返回所有的div元素的最后一个节点的数组
  - `$("div button:only-child")`       返回所有的div中只有唯一一个子节点的所有子节点的数组


- 表单元素选择器：
  - `$(":input")`                  选择所有的表单输入元素，包括`input`,` textarea`, `select` 和 `button`
  - `$(":text") `                    选择所有的text input元素
  - `$(":password")`           选择所有的password input元素
  - `$(":radio")`                   选择所有的radio input元素
  - `$(":checkbox")`            选择所有的checkbox input元素
  - `$(":submit")`               选择所有的submit input元素
  - `$(":image")`                 选择所有的image input元素
  - `$(":reset")`                   选择所有的reset input元素
  - `$(":button")`                选择所有的button input元素
  - `$(":file")`                     选择所有的file input元素
  - `$(":hidden")`               选择所有类型为hidden的input元素或表单的隐藏域


- 表单元素过滤选择器：
  - `$(":enabled")`             选择所有的可操作的表单元素
  - `$(":disabled")`            选择所有的不可操作的表单元素
  - `$(":checked")`            选择所有的被checked的表单元素
  - `$("select option:selected")` 选择所有的select 的子元素中被selected的元素


- 选取一个 name 为”S_03_22″的input text框的上一个td的text值
  - `$("input[@ name =S_03_22]").parent().prev().text()`


- 名字以"S_"开始，并且不是以"\_R"结尾的         
  - ` $("input[@ name ^='S_']").not("[@ name $='_R']") `


- 一个名为 `radio_01`的`radio`所选的值
  - `$("input[@ name =radio_01][@checked]").val()`;


- ` $("A B") ` 查找A元素下面的所有子节点，包括非直接子节点


- ` $("A>B") ` 查找A元素下面的直接子节点


- `$("A+B")` 查找A元素后面的兄弟节点，包括非直接子节点


- `$("A~B")` 查找A元素后面的兄弟节点，不包括非直接子节点



# Solr
**solr通用查询参数：**     
* defType  查询解析器   
* sort filedName ASC|DESC  对查询结果排序
* start num   分页用的   
* rows num    分页用，每页容量   
* fl   filedList  过滤结果字段，用`,`分隔   
* fq  filed:value 对字段的值过滤
* timeAllowed  指定query执行时间    
* omitHeader  去除响应头信息   
* wt  指定查询结果数据格式：json python csv php xml ruby     


**Solr 标准查询参数：**    
* q   标准语法查询语句
* q.op  查询表达式的默认操作符
* df   默认field
* sow  

* 通配符查询    
  `tes*` 匹配：`test` 和 `testing`等

* 模糊查询    
  `roam~1` 模糊查询 匹配距离为1：`roam` 和`foam` 不会匹配`foams` 距离是2


* 邻近查询    
  `"jakara apache"~10` -含义：`jakara` 和 `apache` 相距10个单词

* 范围查询    
  `[]`、`{}`、`{]` ；`[`-包括值在内， `{}` -会将值排除在外

* 相关度    
  `jakarta  apache^4`  - 结果与 `apache` 更相关

* 常数分数查询
  ` <query_clause>^=<score>` 剔除无关的信息；

### Facet 查询
1. ` facet  ` 为true时进行facet（切片）查询
2. ` facet.query `



## JavaTools
### Maven

`mvn install -Dmaven.test.skip=true`  -- 跳过test 编译

`mvn eclipse:eclipse` --创建IDE工程文件

` mvn archetype:generate -DgroupId=com.lhl -DartifactId=mave-learn -Dpachage=com.lhl.mavenlearning -Dversion=1.0-SNAPSHOT ` 命令分析如下：      
1. ` mvn `  调用Maven命令工具
2. `  archetype:generate ` 调用一个MavenGoal，而 ` archetype ` 定义了许多模板， `  archetype:generate ` 将会列出许多的模板供我们选择
3. ` -DgroupId=com.lhl -DartifactId=mave-learn -Dpachage=com.lhl.mavenlearning -Dversion=1.0-SNAPSHOT   `  分别对应：组织名称、项目名称、java包名、项目的版本号;

` mvn install ` 做的事情比较多：编译、测试、打包和安装到本地仓库。

#### Core Concepts
- Maven Plugins and Goals       
  ` mvn archetype:generate ` archetype-是插件（Plugins） ` generate ` -是Goals; **Plugins是一个或一系列goals的集合** ;在上例中带 ` -D... ` 都是此命令的参数
- Maven生命周期      
  ` mvn install ` 包含了几个Maven生命周期：     
  1. ` resources:resources `
  2. ` compiler:compile `
  3. ` resources:testResources `
  4. ` compiler:testCompile `
  5. ` surefire:test `
  6. ` jar:jar `       

在每个生命周期都有一个goal对应着
- Maven配置
- Maven仓库
- Maven依赖管理
- 站点生成和报告

` $ mvn exec:java -Dexec.mainClass=org.sonatype.mavenbook.weather.Main \ -Dexec.arg="12044" ` 运行jar包

` mvn help:describe -Dplugin=exec -Dfull ` mvn hel 查看exec插件

` mvn dependency:tree `  或者 ` mvn dependency:resolve `

` mvn install -Dmaven.test.skip=true ` 跳过测试
` mvn install -X ` debug模式



``` shell
mvn archetype:generate \
  -DgroupId=org.sonatype.mavenbook.simpleweb \
  -DartifactId=simple-webapp \
  -Dpackage=org.sonatype.mavenbook \
  -Dversion=1.0-SNAPSHOT  \
  -DarchetypeCatalog=internal
```



普通项目 ` pom.xml `



```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>org.sonatype.mavenbook.custom</groupId>
	<artifactId>simple-weather</artifactId>
	<version>1.0</version>
	<packaging>jar</packaging>

	<name>simple-weather</name>
	<url>http://maven.apache.org</url>

	<licenses>
		<license>
			<name>Apache 2</name>
			<url>http://www.apache.org/licenses/LICENSE-2.0.txt</url>
			<distribution>repo</distribution>
			<comments>A business-friendly OSS license</comments>
		</license>
	</licenses>

	<organization>
		<name>Sonatype</name>
		<url>http://www.sonatype.com</url>
	</organization>

	<developers>
		<developer>
			<id>jason</id>
			<name>Jason Van Zyl</name>
			<email>jason@maven.org</email>
			<url>http://www.sonatype.com</url>
			<organization>Sonatype</organization>
			<organizationUrl>http://www.sonatype.com</organizationUrl>
			<roles>
				<role>developer</role>
			</roles>
			<timezone>-6</timezone>
		</developer>
	</developers>

	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
	</properties>

	<dependencies>
		<dependency>
			<groupId>log4j</groupId>
			<artifactId>log4j</artifactId>
			<version>1.2.14</version>
		</dependency>
		<dependency>
			<groupId>dom4j</groupId>
			<artifactId>dom4j</artifactId>
			<version>1.6.1</version>
		</dependency>
		<dependency>
			<groupId>jaxen</groupId>
			<artifactId>jaxen</artifactId>
			<version>1.1.1</version>
		</dependency>
		<dependency>
			<groupId>velocity</groupId>
			<artifactId>velocity</artifactId>
			<version>1.5</version>
		</dependency>
		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<version>3.8.1</version>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>org.apache.commons</groupId>
			<artifactId>commons-io</artifactId>
			<version>1.3.2</version>
			<scope>test</scope>
		</dependency>
	</dependencies>
	<build>
		<plugins>
			<!-- Compiler Configuration -->
			<plugin>
				<artifactId>maven-compiler-plugin</artifactId>
				<version>3.3</version>
				<configuration>
					<source>1.8</source>
					<target>1.8</target>
				</configuration>
			</plugin>
			<!-- Unit Test Configuration -->
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-surefire-plugin</artifactId>
				<configuration>
					<skip>true</skip>
					<testFailureIgnore>true</testFailureIgnore>
				</configuration>
			</plugin>
			<!-- 將依賴打入包中 -->
			<plugin>
				<artifactId>maven-assembly-plugin</artifactId>
				<configuration>
					<descriptorRefs>
						<descriptorRef>jar-with-dependencies</descriptorRef>
					</descriptorRefs>
				</configuration>
				<executions>
					<!-- 将assembly 与 package 生命周期绑定 -->
					<execution>
						<id>simple-command</id>
						<phase>package</phase>
						<goals>
							<goal>attached</goal>
						</goals>
					</execution>
				</executions>
			</plugin>
		</plugins>
	</build>
</project>

```

` web pom.xml `


```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>org.sonatype.mavenbook.simpleweb</groupId>
	<artifactId>simple-webapp</artifactId>
	<packaging>war</packaging>
	<version>1.0-SNAPSHOT</version>
	<name>simple-webapp Maven Webapp</name>
	<url>http://maven.apache.org</url>
	<dependencies>
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>servlet-api</artifactId>
			<version>2.4</version>
			<scope>provided</scope>
		</dependency>
		<dependency>
			<groupId>javax.servlet.jsp</groupId>
			<artifactId>jsp-api</artifactId>
			<version>2.0</version>
			<scope>provided</scope>
		</dependency>
		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<version>3.8.1</version>
			<scope>test</scope>
		</dependency>

	</dependencies>
	<build>
		<finalName>simple-webapp</finalName>
		<plugins>
			<plugin>
				<artifactId>maven-compiler-plugin</artifactId>
				<version>3.3</version>
				<configuration>
					<source>1.8</source>
					<target>1.8</target>
				</configuration>
			</plugin>
			<!-- jetty插件 将web项目打成可执行 mvn jetty:run -->
			<plugin>
				<groupId>org.mortbay.jetty</groupId>
				<artifactId>maven-jetty-plugin</artifactId>
			</plugin>
		</plugins>
	</build>
</project>
```


## 数据库
### MySQL
`select user();`  - 查看当前用户

`select database();`  -- 查看当前数据库

`net start mysql`   -- dos 启动

`mysql -h localhost -u root -p password`  - 登录指定服务器、用户、密码

`CREATE DATABASE IF NOT EXISTS shiro DEFAULT CHARSET utf8 COLLATE utf8_general_ci;`  -- 创建数据库，指定字符集UTF-8

`drop database shiro`  --删除数据库 shiro

`source filepath.sql`   执行数据库sql 脚本





























3
