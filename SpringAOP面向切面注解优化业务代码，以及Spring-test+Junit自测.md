### SpringAOP面向切面注解优化业务代码，以及Spring-test+Junit自测

AOP为Aspect Oriented Programming的缩写，意为：面向切面编程，通过预编译方式和运行期动态代理实现程序功能的统一维护的一种技术.AOP是[OOP](http://baike.baidu.com/view/63596.htm)的延续，是软件开发中的一个热点，也是[Spring](http://baike.baidu.com/view/23023.htm)框架中的一个重要内容，是[函数式编程](http://baike.baidu.com/view/1711147.htm)的一种衍生范型。利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的[耦合度](http://baike.baidu.com/view/1599212.htm)降低，提高程序的可重用性，同时提高了开发的效率。在spring AOP中业务逻辑仅仅只关注业务本身，将日志记录，性能统计，安全控制，事务处理，[异常处理](http://baike.baidu.com/view/1072586.htm)等代码从业务逻辑代码中划分出来，通过对这些行为的分离，将它们独立到非业务逻辑的方法中，进而改变这些行为的时候不影响业务逻辑的代码。

当前业务代码缺点：举例UI接口的鉴权， 对于当亲UI接口的鉴权方式很不统一， 散落在各个接口的内部处理中，降低了代码的可读性，耦合性很高，代码结构不清晰，业务与公共方法耦合严重，需要将公共处理与业务处理解耦，通过AOP面向切面注解的方式，只需在接口上添加注解即可实现鉴权，无需接口内部处理，提高接口开发效率，减少冗余代码

Spring AOP的源码及JDK代理、CGLIB代理这里不再分析，聚焦于具体实现

开发对象：Spring容器管理对象，规范统一的接口（后期开发需要关注接口开发，尽量保持规范统一，容易扩展）

具体实现：

1、Pom下导入依赖Jar包

```
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <type>jar</type>
</dependency>
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjrt</artifactId>
    <type>jar</type>
</dependency>
```

2、在application.xml中配置aop代理

通过aop命名空间的<aop:aspectj-autoproxy />声明自动为spring容器中那些配置@aspectJ切面的bean创建代理，织入切面。

```
<aop:aspectj-autoproxy/>
```

3、自定义注解

![img](http://image.huawei.com/tiny-lts/v1/images/1ccc5264fc0e2a2428c9_352x101.png@900-0-90-f.png)
4、编写AOP切面类

![img](http://image.huawei.com/tiny-lts/v1/images/07a13264fc1f5fb31ffd_985x840.png@900-0-90-f.png)
5、定义切点，将自定义注解应用在定义切点处

 

 

   将业务代码与鉴权、日志记录、安全控制、异常处理等事务进行分离，做到业务逻辑与公共事务解耦，减少冗余代码、提高接口开发、业务开发效率，将日志记录、安全控制、异常处理、鉴权等抽成一个统一处理方式，不仅解决了开发效率问题，优化代码结构，减少冗余代码，同样有利于后期维护，问题定位。

修改完成后，可以利用Spring-test+junit  在本地进行自测，之后再端到端进行自验，提高自验效率，基于Spring-test +junit的简单自测实现如下：

1、POM文件下导入依赖的Spring-test JAR包

```
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
    <version>3.2.4.RELEASE</version>
    <scope>provided</scope>
</dependency>
```

![img](http://image.huawei.com/tiny-lts/v1/images/38db7264fc3693611150_1027x104.png@900-0-90-f.png)
3、定义要测试测类对象，以及要切入的方法

![img](http://image.huawei.com/tiny-lts/v1/images/5ab3e264fc3831cec211_417x193.png@900-0-90-f.png)
4、利用Spring-test+junit编写测试demo，运行demo，进行自测

![img](http://image.huawei.com/tiny-lts/v1/images/66b16264fc3a3c890baf_452x327.png@900-0-90-f.png)

如果未走到切面类的方法，则编写切面有BUG,需要本地排查代码问题，通过Spring-test+junit的方式能快速定位代码低级错误，提高开发效率，节省开发时间

如有疑议，请邮件或其他方式联系