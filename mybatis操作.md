## mybatis操作

#### 1.批量删除

##### 接口部分

​    int deleteTaskDetailByTaskIds(@Param("taskIds") List<Integer> taskIds);

##### mapper文件

```xml
<delete id="deleteTaskDetailByTaskIds" parameterType="java.util.List">  DELETE  FROM t_task_detail  WHERE t_task_detail.task_id IN  <foreach collection="taskIds" item="item" open="(" separator="," close=")">    #{item,jdbcType=INTEGER}  </foreach></delete>
```

在接口部分入参用@Param来注解，collection指代入参的名字，item指代集合中的单个对象。

#### 2.多条件查询

##### 接口部分

```java
List<TaskItem> listTaskItemByTaskIdAndStatus(Page<TaskItem> page);
```

##### mapper文件

```java
 <select id="listTaskItemByTaskIdAndStatus" resultMap="BaseResultMapPlus" parameterType="com.xdd.mybatis.Page">  SELECT b.store_name AS storeName,b.app_type_id AS appTypeId,  c.product_name AS productName,c.product_code AS productCode,  c.product_upc AS productUpc ,c.sku_name AS skuName,  c.content,c.`status`,c.memo  FROM t_task_detail b  LEFT JOIN t_task_item c ON b.id = c.task_detail_id  WHERE c.task_id = #{params.taskId, jdbcType = INTEGER}  <if test="params.status != null">      AND c.`status` = #{params.status, jdbcType = INTEGER}  </if>  </select>
```

使用<if test =“xxx ！=null"></if>做非空校验