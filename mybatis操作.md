## mybatis操作

#### 1.批量删除

##### 接口部分

​    int deleteTaskDetailByTaskIds(@Param("taskIds") List<Integer> taskIds);

##### mapper文件

```xml
<delete id="deleteTaskDetailByTaskIds" parameterType="java.util.List"> 
    DELETE  FROM t_task_detail  WHERE t_task_detail.task_id IN  
    <foreach collection="taskIds" item="item" open="(" separator="," close=")">    
        #{item,jdbcType=INTEGER}  
    </foreach>
</delete>
```

在接口部分入参用@Param来注解，collection指代入参的名字，item指代集合中的单个对象。

#### 2.多条件查询

##### 接口部分

```java
List<TaskItem> listTaskItemByTaskIdAndStatus(Page<TaskItem> page);
```

##### mapper文件

```java
 <select id="listTaskItemByTaskIdAndStatus" resultMap="BaseResultMapPlus" parameterType="com.xdd.mybatis.Page">
 SELECT b.store_name AS storeName,b.app_type_id AS appTypeId,  c.product_name AS productName,
c.product_code AS productCode, 
c.product_upc AS productUpc ,
c.sku_name AS skuName, 
c.content,c.`status`,
c.memo  
FROM t_task_detail b  LEFT JOIN t_task_item c
ON b.id = c.task_detail_id
WHERE c.task_id = #{params.taskId, jdbcType = INTEGER}  
<if test="params.status != null">  
AND c.`status` = #{params.status, jdbcType = INTEGER}  
</if> 
</select>
```

使用<if test =“xxx ！=null"></if>做非空校验

#### 3多对多查询

<!--易错注意-->

**在查询两个表中的字段时，如果有字段名称一样，千万要取别名，不然就会出现集合中只有一个元素的情况**

![](C:\Users\Administrator\Desktop\studyNotes\images/1566402402378.png)

```xml
先写一个resultMap，然后再写查询的语句
<resultMap id="roleMap" type="com.wzw.entity.Role">
  <id column="id" property="id" jdbcType="INTEGER"/>
  <result column="name" property="name" jdbcType="VARCHAR"/>
  <collection property="userList" ofType="com.wzw.entity.User">
    <id column="userId" property="id" jdbcType="INTEGER"/>
    <result column="username" property="username" jdbcType="VARCHAR"/>
    <result column="password" property="password" jdbcType="VARCHAR"/>
  </collection>
</resultMap>
```

```xml
<select id="getUserByRoleName" resultMap="roleMap" parameterType="java.lang.Integer">
  SELECT r.id,r.name ,u.id userId,u.username,u.password
  FROM t_user u
  LEFT JOIN t_user_role t ON t.user_id = u.id
  LEFT JOIN t_role r ON r.id = t.role_id
  where r.id = #{id}
</select>
```

