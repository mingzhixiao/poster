## cache缓存

#### 1、公共缓存抽取

```java
@CacheConfig(cacheNames = "emp")
//CacheConfig 可以抽取公共的缓存，在此处就是抽取名为emp的缓存空间

```

#### 2、@cacheable使用

    @Resource
    EmployeeMapper employeeMapper;
    
    /*
    @Cacheable将方法的运行结果进行缓存，以后再要相同的数据，直接从缓存中获取
    org.springframework.boot.autoconfigure.cache.SimpleCacheConfiguration
    使用cacheManager来获取缓存
    * */
    @Cacheable(/*cacheNames = "emp",*/condition = "#a0>1")
     public Employee getEmp(Integer id){
         System.out.println("查询"+id+"号员工");
         Employee employee = employeeMapper.getEmployeeById(id);
         return employee;
     }


​    

#### 3、CachePut的使用

    /*
    
    *CachePut 既调用方法，又更新缓存数据
    
    - */
      @CachePut(/value = "emp"/)
      public Employee updateEmployee(Employee employee){
        System.out.println("员工信息更新");
        employeeMapper.updateEmployee(employee);
        return employee;
      }

​    

#### 4.@CahcheEvict的使用


    /*
    
    - @CacheEvict:缓存清除
    - 默认是在执行方法以后执行
    - beforeInvocation = true//清除缓存实在方法执行之前就清除
    - */
    
    @CacheEvict(/value = "emp" ,/key = "#id"/* beforeInvocation = true*/)
    
    public void deleteEmployee(Integer id){
    
    System.out.println("删除:"+ id + "号员工");
    
    employeeMapper.deleteEmployeeById(id);
    
    }

#### 5@Caching的使用


    /*
    
    - @Caching 定义复杂的缓存功能
    - 作用是能够批量将一些数据以key的方式存入缓存
    - */
    
    @Caching(
    cacheable = {
                @Cacheable(/*value = "emp" ,*/key = "#lastName")
        },
        put = {
                @CachePut(/*value = "emp",*/ key = "#result.id"),
                //将key为id的放入缓存，作用是不用在根据id查询时再查询数据库
                @CachePut(/*value = "emp"*/ key = "#result.email")
                //将key为emil的放入缓存，作用是不用在去查询数据库
        }
        public Employee getEmpByLastName(String lastName){
    
        return employeeMapper.getEmpByLastName(lastName);
    
    }
    )

