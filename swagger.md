#### swagger的使用

##### 1.引入swagger的两个依赖

```
<!-- swagger的依赖 start -->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.9.2</version>
</dependency>

<!--引入ui包-->
<dependency>
    <groupId>com.github.xiaoymin</groupId>
    <artifactId>swagger-bootstrap-ui</artifactId>
    <version>1.8.7</version>
</dependency>
<!-- swagger的依赖 end -->



```

##### 2引入swagger的配置类

```
package com.wzw.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import springfox.documentation.builders.ApiInfoBuilder;
import springfox.documentation.builders.PathSelectors;
import springfox.documentation.builders.RequestHandlerSelectors;
import springfox.documentation.service.ApiInfo;
import springfox.documentation.service.Contact;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotations.EnableSwagger2;

/**
 * @program: drugstore
 * @description: swagger的基本配置
 * @author: wzw
 * @create: 2019-04-18 09:50
 */
@Configuration
@EnableSwagger2
public class MySwagger {
    private ApiInfo apiInfo(){
        Contact wzw = new Contact("明之晓", "", "");
        return new ApiInfoBuilder()
                .title("wzw的文档")
                .contact(wzw)
                .description("drugstore的api")
                .termsOfServiceUrl("http://127.0.0.1:8888/")
                .version("wzw.1.0")
                .build();
    }

    @Bean
    public Docket createRestApi(){
    return new Docket(DocumentationType.SWAGGER_2)
            .apiInfo(apiInfo())
            .groupName("第一版本")
            .select()
            .apis(RequestHandlerSelectors.basePackage("com.wzw.web"))
            .paths(PathSelectors.any())
            .build();
    }

    @Bean
    public Docket createRestApi2(){
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .groupName("第二版本")
                .select()
                .apis(RequestHandlerSelectors.basePackage("com.wzw.web"))
                .paths(PathSelectors.any())
                .build();
    }
}
```

通过以上的配置就可以完成对于swagger的ui界面的访问

http://${host}:${port}/doc.html







3.对于controller中的配置

```
package com.wzw.web;

import com.wzw.service.ICustomerService;
import com.wzw.utils.Page;
import com.wzw.utils.SmallPage;
import com.wzw.vo.CustomerVo;
import io.swagger.annotations.*;
import org.springframework.ui.ModelMap;
import org.springframework.web.bind.annotation.*;
import org.springframework.web.servlet.ModelAndView;

import javax.annotation.Resource;
import java.util.HashMap;
import java.util.Map;

/**
 * @program: drugstore
 * @description: 顾客信息的控制操作
 * @author: wzw
 * @create: 2019-03-27 14:09
 */
@Api(tags = "顾客Api")
@RestController
@RequestMapping("/customer")
public class CustomerController {
    @Resource
    private ICustomerService customerService;

    int limit = 10;
    /**
     * @description 来到顾客信息显示界面
     * @param
     * @throws 
     * @return 
     */
  @ApiOperation(value = "来到顾客信息页面",notes = "查询所有的顾客信息第一页")
  @RequestMapping(value = "/goAll", method = RequestMethod.GET)
    public ModelAndView goCustomer(ModelMap modelMap){
      Page<CustomerVo> customerVoPage = customerService.allCustomerVo(1, limit);
      int[] minAndMax = SmallPage.minAndMax(1, customerVoPage.getTotalPage());
      modelMap.addAttribute("customerVoPage",customerVoPage);
      modelMap.addAttribute("minAndMax",minAndMax);
      return new ModelAndView("customerList");
    }



    /**
     * @description 来到顾客信息添加页面
     * @param
     * @throws
     * @return
     */
    @ApiOperation(value = "来到顾客信息添加页面", notes = "顾客信息第一页")
    @RequestMapping(value = "/goAdd", method = RequestMethod.GET)
    public ModelAndView goAdd(){
        ModelAndView modelAndView = new ModelAndView("customerAdd");
        return modelAndView;
    }


    /**
     * @description 添加顾客信息，修改顾客信息
     * @param
     * @throws
     * @return
     */
    @ApiOperation(value = "添加用户信息", notes = "传入一个用户的信息")
    @PostMapping("/add")
    public Boolean add(/*@RequestBody @ApiParam(value = "Created user object", required = true)*/CustomerVo customerVo){
        customerVo.setStatus(1);
       // Map<String, Object> resultMap = new HashMap<>();
        Boolean result = customerService.insertCustomerVo(customerVo);
        //resultMap.put("customerResult",result);
        System.out.println(result);
        return result;
    }

    @ApiOperation(value = "更新用户信息")
    @PutMapping("/update")
    public Map<String,Object> update(CustomerVo customerVo){
        Map<String, Object> resultMap = new HashMap<>();
            Boolean result = customerService.updateCustomerVo(customerVo);
            resultMap.put("success",result);
        return resultMap;
    }

    @ApiOperation(value = "删除用户信息")
    @ApiImplicitParam(name = "id", required = true)
    @DeleteMapping("/delete/{id}")
    public Boolean delete(@PathVariable("id") int id){
       // Map<String, Object> resultMap = new HashMap<>();
        Boolean deleteCustomerVo = customerService.deleteCustomerVo(id);
       // resultMap.put("success",deleteCustomerVo);
        return deleteCustomerVo;
    }

    @ApiOperation(value = "查询所有顾客信息")
    @GetMapping("/all")
    public ModelAndView all(ModelMap modelMap,@ApiParam(required = true) int page){
        Page<CustomerVo> customerVoPage = customerService.allCustomerVo(page, limit);
        int[] minAndMax = SmallPage.minAndMax(page, customerVoPage.getTotalPage());
        modelMap.addAttribute("customerVoPage",customerVoPage);
        modelMap.addAttribute("minAndMax",minAndMax);
        return new ModelAndView("customerList");
    }

    @ApiOperation(value = "获取客户信息", notes = "根据用户的id来获取用户信息")
    @ApiImplicitParam(name = "id", value = "用户的id", required = true, dataType = "Integer", paramType = "path")
    @GetMapping("/getById/{id}")
    public Map<String, Object> getCustomerById(@PathVariable int id){
        CustomerVo customerBynNum = customerService.getCustomerBynNum(id);
        Map<String, Object> hashmap = new HashMap<>();
        hashmap.put("customer",customerBynNum);
        return hashmap;
    }


}
```

以上便是一个完整的过程。

![1555573052205](C:\Users\wuzhuwei\AppData\Roaming\Typora\typora-user-images\1555573052205.png)