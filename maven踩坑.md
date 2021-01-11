### maven依赖踩坑记录

##### 1.1错误现象

```
Failed to read artifact descriptor for com.xdd.cshop.manager:xdd-cshop-manager-remote:jar:1.1.4.1-SNAPSHOT: Could not find artifact com.xdd.cshop.manager:xdd-cshop-manager:pom:1.1.4.1-SNAPSHOT in xdd-mzone-nexus 
```

##### 1.2错误图片

![](C:\Users\wuzhuwei\Desktop\studyNotes\images\maven\errorView.png)



##### 1.2异常的父子模块依赖

![](C:\Users\wuzhuwei\Desktop\studyNotes\images\maven\errorStruct.png)



##### 1.3 正常的父子模块依赖

![](C:\Users\wuzhuwei\Desktop\studyNotes\images\maven\normalStruct.png)

##### 1.4解决方式

 将子模块所依赖的父模块与子模块一起提交

