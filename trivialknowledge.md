# Java
* ajax 传数组到后台：     
  ```javascript
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

### java.util.concurrent 源码
#### Thread
* Thread.interrupted()- Test 当前 **运行的 Thread** 是否被中断, 会clear thread status
* thread.isInterrupt() - Test this Thread is interrupted；

#### Copy-On-Write 之 `CopyOnWriteArrayList`
Copy-On-Write 有 ` CopyOnWriteArrayList ` 和 ` CopyOnWriteArraySet `；顾名思义在对容器进行写操作时复制新容器——先往新的容器中添加、删除数据，操作完成后将原容器的引用指向新容器；这样不需要锁就可以进行并发读容器操作。

###### 实现原理——在写操作时加锁
```java
  //jdk 1.8
  public E set(int index, E element) {
      final ReentrantLock lock = this.lock;
      lock.lock();
      try {
          Object[] elements = getArray();
          E oldValue = get(elements, index);

          if (oldValue != element) {
              int len = elements.length;
              //复制 新数组  
              Object[] newElements = Arrays.copyOf(elements, len);
              newElements[index] = element;
              // 把原数组引用指向新数组
              setArray(newElements);
          } else {
              // Not quite a no-op; ensures volatile write semantics
              setArray(elements);
          }
          return oldValue;
      } finally {
          lock.unlock();
      }
  }

  final void setArray(Object[] a) {
      // 内部数组结构
      array = a;
  }
```
CopyOnWrite的适用读多写少的并发场景。
###### 缺点
1. 内存占用问题——写操作时2个对象存在内存中。解决：压缩容器元素；使用其他并发容器
2. 数据一致性问题——只能保证 **最终一致** ，不能保证数据实时一致。

#### Copy-On-Write 之 `CopyOnWriteArrayList`

```java
  //jdk 1.8
  public void add(int index, E element) {
      final ReentrantLock lock = this.lock;
      lock.lock();
      try {
          Object[] elements = getArray();
          int len = elements.length;
          if (index > len || index < 0)
              throw new IndexOutOfBoundsException("Index: "+index+
                                                  ", Size: "+len);
          // 新数组                                        
          Object[] newElements;
          // 移动元素数量
          int numMoved = len - index;
          // 在数组末位插入
          if (numMoved == 0)
              newElements = Arrays.copyOf(elements, len + 1);
          else {
              newElements = new Object[len + 1];
              // 复制前 index 元素
              System.arraycopy(elements, 0, newElements, 0, index);
              // 复制 剩余的元素
              System.arraycopy(elements, index, newElements, index + 1,
                               numMoved);
          }
          // 特定位置插入元素
          newElements[index] = element;
          // 原数组引用指向新数组
          setArray(newElements);
      } finally {
          lock.unlock();
      }
  }
```



### Spring Boot
Spring Boot 四个核心：
- 自动配置 ——基于 Spring的条件化配置特性，spring-boot-autoconfigure.jar包含许多配置类
- 起步（starter）依赖 ——jar自动导入
- 命令行界面 ——开发脚手架
- Actuator ——监控

#### 自动配置
spring-boot-autoconfigure.jar 包含许多配置类，实现了 ` Condition ` 接口，覆盖 ` matches() ` 方法。

** 3.2 通过属性文件外置配置 **
可以使用 ` application.properties ` 文件，或者 ` application.yaml ` 除此外还有：
1. 命令行参数
2. ` java:comp/env ` 里的JNDI属性
3. JVM系统属性
4. 操作系统环境变量
5. 随机生成的带 random.* 前缀的属性；例如：` ${random.long} `
6. 程序外的 ` application.properties ` 或者 ` application.yml ` 文件
7. 打包在应用程序内的 `application.properties` 或者 ` appliaction.yml ` 文件
8. 通过 ` @PropertySource ` 标注的属性源
9. 默认属性


` @SpringApplicationConfiguration ` 用法和 ` @ContextConfiguration ` 大致相同,但是它不知加载Spring程序的上下文，还会开启日志、加载外部属性，以及其他Spring Boot特性。
相当于 ` @Configuration ` , ` @EnableAutoConfiguration ` , ` @ComponentScan ` 三者组合。

` @EnableAutoConfiguration ` 是Boot 的特有注解,这个注解在其他包之上，这样它会默认扫描子包下的组件。

` @WebAppConfiguration ` 表明是个 ` WebApplicationContext `

` spring-boot-starter-jetty ` 和 ` spring-boot-starter-tomcat ` 是Spring Boot 的内嵌的 ` jetty ` 和 ` tomcat `

` @Import ` 和 ` @ImportResource ` 可以加载额外的配置项内容

` @EnableAutoConfiguration(exclude={DataSourceAutoConfiguration.class}) ` 可以关闭制定的自动配置项内容

当类路径下的文件有变化时 ， ` spring-boot-devtools ` 会自动重启服务

` @WebMvcTest ` 将会自动配置 SpringMVC 结构，只会扫描 ` @Controller ` , ` @ControllerAdvice ` , ` @JsonComponent` , ` Filter ` ,  `WebMvcConfigurer ` 和 ` HandlerMethodArgumentResolver ` 而普通的 ` @Component ` 不会扫描到。

```java
@RunWith(SpringRunner.class)
@WebMvcTest(UserVehicleController.class)
public class MyControllerTests {
  @Autowired
  private MockMvc mvc;
  @MockBean
  private UserVehicleService userVehicleService;
  @Test
  public void testExample() throws Exception {
      given(this.userVehicleService.getVehicleDetails("sboot"))
        .willReturn(new VehicleDetails("Honda", "Civic"));
      this.mvc.perform(get("/sboot/vehicle").accept(MediaType.TEXT_PLAIN))
        .andExpect(status().isOk()).andExpect(content().string("Honda Civic"));
  }
}
```

```java
@RunWith(SpringRunner.class)
@WebMvcTest(UserVehicleController.class)
public class MyHtmlUnitTests {
  @Autowired
  private WebClient webClient;
  @MockBean
  private UserVehicleService userVehicleService;
  @Test
  public void testExample() throws Exception {
      given(this.userVehicleService.getVehicleDetails("sboot"))
          .willReturn(new VehicleDetails("Honda", "Civic"));
      HtmlPage page = this.webClient.getPage("/sboot/vehicle.html");
      assertThat(page.getBody().getTextContent()).isEqualTo("Honda Civic");
  }
}

```

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
* q   标准语法查询语句, q="new house" 短语查询，不会分词
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

### Highlight 查询
参数说明：     
  * `hl`-开启高亮
  * `hl.fl`--高亮的字段
  * `hl.simple.pre` 和 `hl.simple.post` 高亮的自定义格式




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















# 高性能MySQL
## 第六章 查询性能优化
表结构、建立好的索引、查询性能优化。

### 优化数据访问
- 确定需要的数据，查询中不带不需要的数据（行，用Limit）和列，将需要重复查询的相同数据缓存起来。   
- 是否扫描了额外的记录，响应时间、扫描行数、返回行数

查询扫描大量数据但只返回了少数的行，用以下方法优化：      
- 使用索引覆盖扫描，把所有需要的列放入索引中。    
- 改变库表结构-单独的汇总表
- 重写查询语句

### 重构查询的方式
- 单个复杂查询 or 多个简单查询      
- 切分查询    
- 分解关联查询      

### 查询执行的基础





# Mybatis
## `#` 和 `$` 的区别
`#` 会被当成占位符，进行预编译,将传入的值自动加上 `""`，能有效的预防sql注入 ；`$` 中的变量会当成字符串直接显示生成在sql中，会存在sql注入。

## insert 标签

``` xml
<!-- 插入前生产ID -->
<insert id="insertAuthor">
  <selectKey keyProperty="id" resultType="int" order="BEFORE">
    select CAST(RANDOM()*1000000 as INTEGER) a from SYSIBM.SYSDUMMY1
  </selectKey>
  insert into Author
    (id, username, password, email,bio, favourite_section)
  values
    (#{id}, #{username}, #{password}, #{email}, #{bio}, #{favouriteSection,jdbcType=VARCHAR})
</insert>

<!-- 批量插入 -->
<insert id="batchInsertUsers" parameterType="java.util.List">  
    insert into mhc_user(userName,password) values  
    <foreach collection="list" item="item" index="index" separator=",">  
        (#{item.userName},#{item.password})  
    </foreach>  
</insert> 

```

## sql 标签

``` xml
<sql id="userColumns"> ${alias}.id,${alias}.username,${alias}.password </sql>

<select id="selectUsers" resultType="map">
  select
    <include refid="userColumns"><property name="alias" value="t1"/></include>,
    <!-- 会被解析为 t.id ... -->
    <include refid="userColumns"><property name="alias" value="t2"/></include>
  from some_table t1
    cross join some_table t2
</select>
```

## 参数

`#{property,javaType=int,jdbcType=NUMERIC}` `javaType` 是java 的类型，`jdbcType` 是数据库类型；

**在插入时，如果一个列允许为空，并可能传入空值进来则必须指定 `jdbcType` 类型**




















3
