# springboot整合（原生）dubbo和mybatis

#### 1.一般所言的dubbo是三部分，分别是provider，api，consume。

这里我来总结一下实际工作中所分的块：

remote：provider块所暴露出来的接口 即是service的接口：xxxService

provider：提供者，也就是service的实现类  :xxxServiceImpl

core:由提供者所分离出来操作数据库部分 xxxMapper.java ,xxxMapper.xml，以及Javabean

web：即消费者，controller类

#### 2.编写中所填过的坑

我编写的demo是分为api，provider，consume这三块

##### a.api中的坑

第一个坑：由于mapper.xml是存放在Java/com.wzw.***下，在maven中，Java中默认是编写Java代码的，因此扫描不到xml文件。

在这样简单的项目中 dubbo的依赖如下

第二个坑：dubbo中传输的数据要序列化，因此得要让javabean继承一个Serializable，以此实现序列化

java.lang.IllegalStateException: Serialized class com.wzw.myapi.entity.Employee must implement java.io.Serializable（没有序列化的后果）



##### b.provider中的坑

第一个坑：

在启动类中加上如下三个扫描

```
@ImportResource("dubbo_config/provide.xml") //扫描dubbo的配置文件 （不可少）
@MapperScan("com.wzw.myapi.domain") //扫描mapper的包（不可少） 少了就报找不到mapper错误
/*@ComponentScan("com.wzw")*/ 尽量加上这个组件扫描

第二个坑：
这里的xxxService需要绑定dubbo中所配置的暴露出来的接口的ref

以下是在provider中的dubbo配置文件
<beans xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:dubbo="http://dubbo.apache.org/schema/dubbo"
       xmlns="http://www.springframework.org/schema/beans"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.3.xsd
       http://dubbo.apache.org/schema/dubbo http://dubbo.apache.org/schema/dubbo/dubbo.xsd">

  
    <dubbo:application name="provider"/>
    
    <dubbo:registry address="zookeeper://127.0.0.1:2181"/>
  
    <dubbo:protocol name="dubbo" port="20880"/>
  	<!-- 暴露出来的接口  以及暴露出的 引用传递 在xxxServiceImpl中需要用绑定ref的值，consume中对应的接受的也应绑定ref的值 -->
    <dubbo:service interface="com.wzw.myapi.Service.EmployeeService" ref="employeeService"/> 
</beans>



```

##### c.consumer中的坑

第一个坑：

加上如下两个扫描

```
@ImportResource("dubbo_config/provide.xml")
@ComponentScan("com.wzw")
```

以下是在consume中的dubbo配置文件

<beans xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:dubbo="http://dubbo.apache.org/schema/dubbo"
       xmlns="http://www.springframework.org/schema/beans"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.3.xsd
       http://dubbo.apache.org/schema/dubbo http://dubbo.apache.org/schema/dubbo/dubbo.xsd">

    <dubbo:application name="consume_wzw"/>
      
    <dubbo:registry address="zookeeper://127.0.0.1:2181"/>
    
    <dubbo:reference id="employeeService" check="false" interface="com.wzw.myapi.Service.EmployeeService"/>
</beans>



#### 3依赖不可少

```
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>dubbo</artifactId>
    <version>2.6.6</version>
</dependency>

<dependency>
    <groupId>org.apache.zookeeper</groupId>
    <artifactId>zookeeper</artifactId>
    <version>3.4.9</version>
</dependency>

<dependency>
    <groupId>com.101tec</groupId>
    <artifactId>zkclient</artifactId>
    <version>0.11</version>
</dependency>
```





```java
<dependency>
    <groupId>org.apache.curator</groupId>
    <artifactId>curator-framework</artifactId>
    <version>2.12.0</version>
</dependency> //少了这个会报 org.springframework.beans.factory.BeanCreationException: Error creating bean with name


<dependency>
	<groupId>io.netty</groupId>
	<artifactId>netty-all</artifactId>
	<version>4.1.32.Final</version>
</dependency> //少了这个会报 com.alibaba.dubbo.rpc.RpcException: Failed to invoke the method getEmployeeByID 

```





