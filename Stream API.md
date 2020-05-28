# Stream API

Sream是什么？

数据渠道，用于操作数据源（集合，数组等）所生成的元素序列。

**集合讲的是数据，Stream讲的是计算**

> 注意

- Stream自己不会存储元素
- Stream不会改变源对象，相反会返回一个持有结果的新Stream
- Stream操作是延迟的，需要结果的时候才执行

> 操作步骤

- 1.创建Stream

   一个数据源（集合，数组）获得一个流

- 2.中间操作

  中间操作链，对数据源的数据进行处理

- 3.终止操作

   一旦执行终止操作，就执行中间操作链，并产生结果之后不会再被使用

> 代码实现

- 创建方式 
  
  - 1.通过集合
  
  ```java
   List<Person> list = PersonData.getPerson();
   //返回一个顺序流
   Stream<Person> stream = list.stream();
   //返回一个并行流
   Stream<Person> personStream = list.parallelStream();
  ```
  
  - 2.通过数组
  
  ```java
  int[] a = new int[]{1,2,3,4,5};
  //调用arrays类的static方法
  IntStream stream = Arrays.stream(a);
  
  Person za = new Person(1, "za");
  Person da = new Person(2, "da");
  Person[] people = {za, da};
  Stream<Person> stream1 = Arrays.stream(people);
  ```
  
  - 3.通过stream的of
  
  ```java
  Stream<Integer> stream = Stream.of(1, 2, 3, 4, 5, 6);
  ```

- 中间操作

  - 1.筛选与切片
  
  ```java
  List<Person> list = PersonData.getPerson();
          //filter（） 过滤操作
          System.out.println("====filter====");
          list.stream().filter(e -> e.getId() > 1002)
                  .forEach(System.out::println);
  
          System.out.println("====limit====");
          //limit() 截断流 -- 使元素不超过给定数量
          list.stream().limit(3).forEach(System.out::println);
  
          //skip（） 跳过几个元素
          System.out.println("====skip====");
          list.stream().skip(3).forEach(System.out::println);
  
          //distinct () -- 筛选，通过hashcode()和equals()去重
          System.out.println("====distinct====");
          list.add(new Person(1001,"1001"));
          list.add(new Person(1001,"1001"));
          list.add(new Person(1001,"1001"));
          list.add(new Person(1001,"1001"));
  
          list.stream().distinct().forEach(System.out::println);
  ```
  
  - 2.映射
  
  ```java
   //map(Function f) --接受一个函数作为参数，将元素转换成其他形式
           // 或者提取信息，该函数会映射到每个元素上，并将其映射成一个新的元素
           List<String> list = Arrays.asList("aa", "bb", "cc");
           list.stream().map(str -> str.toUpperCase()).forEach(System.out:: println);
  
           List<Person> person = PersonData.getPerson();
           person.stream()
                   .map(Person::getId)
                   .filter(id -> id>1002)
                   .forEach(System.out::println);
           //flatMap(Function f) - 接受一个函数作为参数,将流中的每个值都换成另一个流，
           //然后把所有流连成一个流.相当于addAll(),打开集合套集合
  ```
  
  - 3.排序
  
  ```java
   //sorted -- 自然排序
          List<Integer> list = Arrays.asList(12, 15, 45, 65, 14, 54, 21);
          list.stream().sorted().forEach(System.out::println);
  
          //sorted(Comparator<? super P_OUT> comparator) 定制排序
          List<Person> personList = PersonData.getPerson();
          personList.stream()
                  .sorted((o1,o2) -> Integer.compare(o1.getId(),o2.getId()))
                  .forEach(System.out::println);
  ```

- stream终止操作

  - 1.匹配与查找

  ```java
   List<Person> personList = PersonData.getPerson();
          //allMatch 都通过才为true
          boolean b = personList.stream().allMatch(e -> e.getId() > 1001);
          System.out.println("====allMatch-->"+b);
  
          boolean b1 = personList.stream().anyMatch(e -> e.getId() > 1001);
          System.out.println("====anyMatch-->"+b1);
  
          boolean b2 = personList.stream().noneMatch(e -> e.getName().startsWith("张"));
          System.out.println("====noneMatch-->"+b1);
  
          Optional<Person> first = personList.stream().findFirst();
          System.out.println("====findFirst-->"+first);
  
          Optional<Person> any = personList.stream().findAny();
          System.out.println("====findAny-->"+any);
  
          //count个数
          long count = personList.stream().filter(e -> e.getId() > 1002).count();
          System.out.println("====count-->"+count);
  
          Optional<Integer> max = personList.stream().map(e -> e.getId()).max(Double::compare);
          System.out.println("====max-->"+max);
  
          Optional<Person> min = personList.stream().min((o1, o2) -> Double.compare(o1.getId(), o2.getId()));
          System.out.println("====min-->"+min);
  
          personList.stream().forEach(System.out::println);
  ```

  - 2.规约

  ```java
  //reduce()--可以流中元素反复结合起来，得到一个值，返回Optional<Person>
          List<Integer> list = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
          //reduce(T identity--初始值, BinaryOperator<T> accumulator);
          Integer reduce = list.stream().reduce(0, Integer::sum);
          System.out.println(reduce);
  
          //Optional<T> reduce(BinaryOperator<T> accumulator);
          List<Person> personList = PersonData.getPerson();
          Stream<Integer> integerStream = personList.stream().map(Person::getId);
          System.out.println(integerStream.reduce(Integer::sum));
  ```

  - 3.收集

  ```java
  List<Person> person = PersonData.getPerson();
          List<Person> collect = person.stream().filter(e -> e.getId() > 1002).collect(Collectors.toList());
          collect.forEach(System.out::println);
  ```

# 2.Mybatis

> 什么是MyBatis

- MyBatis 是一款优秀的**持久层框架**,一个半自动化的**ORM框架** 

- MyBatis 避免了几乎所有的 JDBC 代码和手动设置参数以及获取结果集的过程
- MyBatis 可以使用简单的 XML 或注解来配置和映射原生信息，将接口和 Java 的 实体类 【Plain Old Java Objects,普通的 Java对象】映射成数据库中的记录。
- MyBatis 本是apache的一个开源项目ibatis, 2010年这个项目由apache 迁移到了google code，并且改名为MyBatis 。
- 2013年11月迁移到**Github** .
- Mybatis官方文档 : http://www.mybatis.org/mybatis-3/zh/index.html
- GitHub : https://github.com/mybatis/mybatis-3

> MyBatis的优点

- 简单易学：本身就很小且简单。没有任何第三方依赖，最简单安装只要两个jar文件+配置几个sql映射文件就可以了，易于学习，易于使用，通过文档和源代码，可以比较完全的掌握它的设计思路和实现。
- 灵活：mybatis不会对应用程序或者数据库的现有设计强加任何影响。sql写在xml里，便于统一管理和优化。通过sql语句可以满足操作数据库的所有需求。
- 解除sql与程序代码的耦合：通过提供DAO层，将业务逻辑和数据访问逻辑分离，使系统的设计更清晰，更易维护，更易单元测试。sql和代码的分离，提高了可维护性。
- 提供xml标签，支持编写动态sql。

## 1.Mybatis第一个程序

1.1创建maven项目

![1590592032760](E:\typoraImgs\1590592032760.png)

1.2导入相关依赖

```xml
<dependencies>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.2</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.20</version>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>
    </dependencies>
```

1.3配置文件 mybatis-config.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=true&amp;useUnicode=true&amp;characterEncoding=utf8&amp;serverTimezone=Asia/Shanghai"/>
                <property name="username" value="root"/>
                <property name="password" value="root"/>
            </dataSource>
        </environment>
    </environments>
<!--    每一个mapper.xml文件都需要mybatis核心配置文件中注册-->
    <mappers>
        <mapper resource="com/zhang/dao/UserMapper.xml"/>
    </mappers>
</configuration>
```

1.4编写MyBatis工具类

```java
private static SqlSessionFactory sqlSessionFactory;
   static {
       try {
           String resource = "mybatis-config.xml";
           InputStream inputStream = Resources.getResourceAsStream(resource);
           sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
      } catch (IOException e) {
           e.printStackTrace();
      }
  }
   //获取SqlSession连接
   public static SqlSession getSession(){
       return sqlSessionFactory.openSession();
  }
```

## 2.核心配置解析

### 2.1核心配置文件

mybatis-config.xml

MyBatis 的配置文件包含了会深深影响 MyBatis 行为的设置和属性信息。 配置文档的
顶层结构如下：

```xml
▪ configuration（配置）
o  properties（属性）
o  settings（设置）
o  typeAliases（类型别名）
o  typeHandlers（类型处理器）
o  objectFactory（对象工厂）
o  plugins（插件）
o  environments（环境配置）
▪  environment（环境变量）
▪  transactionManager（事务管理器）
▪  dataSource（数据源）
o  databaseIdProvider（数据库厂商标识）
o  mappers（映射器）
```

### 2.2 环境配置（environments）

MyBatis 可以配置成适应多种环境，这种机制有助于将 SQL 映射应用于多种数据库之中， 现实情况下有多种理由需要这么做。例如，开发、测试和生产环境需要有不同的配置；或者想在具有相同 Schema 的多个生产数据库中使用相同的 SQL 映射。还有许多类似的使用场景。

**尽管可以配置多个环境，但每个 SqlSessionFactory 实例只能选择一种环境。**

Mybatis默认事务管理器JDBC，默认连接池POOLED

### 2.3、属性

通过配置文件实现引入配置文件

> 编写一个配置文件

```properties
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:3306/mybatis?useSSL=true&useUnicode=true&characterEncoding=utf8&serverTimezone=Asia/Shanghai
username=root
password=root
```

> 在核心配置文件中映射

```xml
<properties resource="db.properties"/>
```

- 可以引入外部指定文件
- 可以在其中增加一些属性
- 如果两个文件有同一个字段，有限使用外部配置文件

## 2.4.类型别名（typeAliases）

- 类型别名可为 Java 类型设置一个缩写名字
- 意在降低冗余的全限定类名书写

```xml
<select id="queryAll" resultType="User">
    select * from user
  </select>

<!--mybatis-config.xml中起别名-->
    <typeAliases>
        <typeAlias type="com.zhang.pojo.User" alias="User"></typeAlias>
    </typeAliases>
```

- 也可以指定一个包名，MyBatis 会在包名下面搜索需要的 Java Bean，比如，在没有注解的情况下，会使用 Bean 的首字母小写的非限定类名来作为它的别名。

```xml
<typeAliases>
        <typeAlias type="com.zhang.pojo“></typeAlias>
    </typeAliases>
```

- 实体类比较少使用第一种方式，实体类多使用第二种

第一种可以DIY别名，第二种可以通过注解

```java
@Alias("author")
public class Author {
    ...
}
```

## 2.5.设置

日志实现

|         |                                                       |       |
| ------- | ----------------------------------------------------- | ----- |
| logImpl | 指定 MyBatis 所用日志的具体实现，未指定时将自动查找。 | SLF4J |

SLF4J | LOG4J | LOG4J2 | JDK_LOGGING | COMMONS_LOGGING | STDOUT_LOGGING | NO_LOGGING

## 2.6.映射器（mappers）

方式一：

```xml
<!-- 使用相对于类路径的资源引用 -->
<mappers>
  <mapper resource="org/mybatis/builder/AuthorMapper.xml"/>
  <mapper resource="org/mybatis/builder/BlogMapper.xml"/>
  <mapper resource="org/mybatis/builder/PostMapper.xml"/>
</mappers>
```

方式二：使用class文件绑定注册

```xml
<!-- 使用映射器接口实现类的完全限定类名 -->
<mappers>
  <mapper class="org.mybatis.builder.AuthorMapper"/>
  <mapper class="org.mybatis.builder.BlogMapper"/>
  <mapper class="org.mybatis.builder.PostMapper"/>
</mappers>

注：
- 接口和mapper配置文件必须同名
- 接口和mapper配置文件必须在同一个包下
```

方式三：使用扫描包进行注入绑定

```xml
<!-- 将包内的映射器接口实现全部注册为映射器 -->
<mappers>
  <package name="org.mybatis.builder"/>
</mappers>

注：
- 接口和mapper配置文件必须同名
- 接口和mapper配置文件必须在同一个包下
```

## 2.7.作用域（Scope）和生命周期

![1590675099131](E:\typoraImgs\1590675099131.png)

不同作用域和生命周期类别是至关重要的，因为错误的使用会导致非常严重的并发问题。

**提示** **对象生命周期和依赖注入框架**

依赖注入框架可以创建线程安全的、基于事务的 SqlSession 和映射器，并将它们直接注入到你的 bean 中，因此可以直接忽略它们的生命周期。 如果对如何通过依赖注入框架使用 MyBatis 感兴趣，可以研究一下 MyBatis-Spring 或 MyBatis-Guice 两个子项目。

#### SqlSessionFactoryBuilder

- 这个类可以被实例化、使用和丢弃，一旦创建了 SqlSessionFactory，就不再需要它了。
- 局部变量

#### SqlSessionFactory

- 一旦被创建就应该在应用的运行期间一直存在，没有任何理由丢弃它或重新创建另一个实例。
- 最简单的就是使用**单例模式**或者**静态单例模式**。

#### SqlSession

- 连接到连接池的一个请求，非线程安全
- 作用域是请求或者方法作用域
- 用完之后关闭，避免资源被占用

![1590675855771](E:\typoraImgs\1590675855771.png)

每一个mapper代表一个业务。

## 3.ResultMap

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.zhang.dao.UserMapper">

    <resultMap id="userMap" type="User">
        <result column="id" property="id" />
        <result column="phone_num" property="pwd"/>
    </resultMap>

    <select id="queryAll" resultMap="userMap">
    select * from user
  </select>
</mapper>
```

## 4.日志

- LOG4J
- STDOUT_LOGGING

### 4.1标准日志实现

```xml
<!--日誌設置了，STDOUT_LOGGING 标准日志工厂实现-->
    <settings>
        <setting name="logImpl" value="STDOUT_LOGGING"/>
    </settings>
```

### 4.2 LOG4J

- Log4j是[Apache](https://baike.baidu.com/item/Apache/8512995)的一个开源项目，通过使用Log4j，我们可以控制日志信息输送的目的地是[控制台](https://baike.baidu.com/item/控制台/2438626)、文件、[GUI](https://baike.baidu.com/item/GUI)组件

- 导入jar

```xml
<dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.17</version>
        </dependency>
```

- 配置文件编写

```xml
#将等级为DEBUG的日志信息输出到console和file这两个目的地，console和file的定义在下面的代码
log4j.rootLogger=DEBUG,console,file

#控制台输出的相关设置
log4j.appender.console = org.apache.log4j.ConsoleAppender
log4j.appender.console.Target = System.out
log4j.appender.console.Threshold=DEBUG
log4j.appender.console.layout = org.apache.log4j.PatternLayout
log4j.appender.console.layout.ConversionPattern=[%c]-%m%n

#文件输出的相关设置
log4j.appender.file = org.apache.log4j.RollingFileAppender
log4j.appender.file.File=./log/kuang.log
log4j.appender.file.MaxFileSize=10mb
log4j.appender.file.Threshold=DEBUG
log4j.appender.file.layout=org.apache.log4j.PatternLayout
log4j.appender.file.layout.ConversionPattern=[%p][%d{yy-MM-dd}][%c]%m%n

#日志输出级别
log4j.logger.org.mybatis=DEBUG
log4j.logger.java.sql=DEBUG
log4j.logger.java.sql.Statement=DEBUG
log4j.logger.java.sql.ResultSet=DEBUG
log4j.logger.java.sql.PreparedStatement=DEBUG
```

- 配置log4j的日志实现

```xml
<settings>
   <setting name="logImpl" value="LOG4J"/>
</settings>
```

## 5.分页

### 5.1使用mybatis分页

接口

```java
List<User> getUserByLimit(Map<String,Integer> map);
```

mapper.xml

```xml
 <!--resultType - 映射到实体类
        resultMap - 字段映射
    -->
    <select id="getUserByLimit" resultType="User" parameterType="map">
        select * from user limit #{startIndex},#{pageSize}
    </select>
```

测试

```java
@Test
    public void testLimit(){
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        Map<String, Integer> map = new HashMap<>();
        map.put("startIndex",0);
        map.put("pageSize",2);
        mapper.getUserByLimit(map);
        sqlSession.close();
    }
```

### 5.2.RowBounds分页

代码层面实现

接口

```java
List<User> getUserRowBounds();
```

mapper.xml

```xml
<select id="getUserRowBounds" resultType="com.zhang.pojo.User">
   select * from user
</select>
```

测试

```java
@Test
    public void testRowBound(){
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        //RowBound 实现分页
        RowBounds rowBound = new RowBounds(1,2);
        List<Object> list = sqlSession.selectList("com.zhang.dao.UserMapper.getUserRowBounds",null,rowBound);
        list.forEach(System.out::println);
        sqlSession.close();
    }
```

## 6.使用注解开发

### 6.1 面向接口开发

​		**解耦**

### 6.2使用注解开发

