## springboot_mybatis的两种使用方式

### 纯接口方式：

#### controller层编写：

    @GetMapping("/user/{id}")
    public User getUserById(@PathVariable("id") Integer id){
        return userMapper.getUserById(id);
    }
#### 接口编写：

    @Select("select username ,password,age,habbit from user where id = #{id}")
    public User getUserById(Integer id);



## 接口方式+xxxMapper.xml方式：

#### controller层编写：

```
@GetMapping("/user/{id}")
public User getUserById(@PathVariable("id") Integer id){
    return userMapper.getUserById(id);
}
```

####  接口编写：

```
public User getUserById(Integer id);
```

#### xxxMpper.xm编写：

    < select id="getUserById" resultType="com.wzw.springboot_day03.bean.User">
       select * from user where id = #{id}
    </select>
