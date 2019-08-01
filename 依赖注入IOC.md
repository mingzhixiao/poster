### 依赖注入

#### 1.建立一个类

```java
public class Person(){

private String name;//姓名

private int age;//年龄

public setName(Stirng name){

this.name;

}

}.../
```

#### 2.将类配置进容器中

```xml
<bean id = "person" name = "com.wzw.Person">
  <property name = "name" value = "zhangsan"></property>
  <property name = "age" value = "18"></property>
</bean>
```

#### 3.通过容器来创建一个类，不需要手动new（运用了反射的原理）

```java
ApplicationContext applicationContext = new FileSystemXmlApplicationContext("applicationContext.xml");
Person person = (Person)application.getBean(person);
person.getPet（）;
```

   eg:在这里以spring作为IOC的这个框架容器来解释（目前只知道spring是IOC容器）

->建立人物类，注入到容器当中；->建立宠物类，注入到容器当中；（这两个建立是不分前后的）

这里有一个需求：建立一个人物对象。

​		由于这两个类都在容器当中，相当于是容器类的属性，不再需要自己new出宠物对象，只需要将宠物对象入注入到任务对象中去。

​	    在spring中，将mybatis作为容器的内容，因此操作jdbc的内容就已经注入到了容器当中，springMVC作为视图控制层进入容器，现在要获取数据库中的类容，就是拼接mybatis和springMVC。

普通模式：因为视图控制层是高层，所以在试图控制层中需要建立一个jdbc层的对象，通过这个对象中的方法来使实现对数据库的操作。

IOC模式：jdbc层的类是容器中的一部分，只需要将jdbc注入到springMVC中就行





//优秀eg网址：

https://blog.csdn.net/it_man/article/details/4402245