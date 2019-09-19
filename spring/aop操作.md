#### aop操作

```java
package com.wzw.config;
import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.After;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Component;
import org.springframework.web.bind.annotation.RequestMapping;
import javax.annotation.Resource;
import javax.servlet.http.HttpServletRequest;
import java.lang.reflect.Method;
import java.util.Date;


/**
 * @Description TODO
 * @Date 2019/9/3 8:16
 * @Created by wzw
 */

@Aspect
@Component
public class AopLog {
    private Date visitTime; //开始访问时间
    private Class clazz;//访问的类
    private Method method;//访问的方法

    private Logger logger = LoggerFactory.getLogger(this.getClass());

    @Resource
    private HttpServletRequest request;


    @Before("execution(* com.wzw.web..*.*(..))")
    public void doBefore(JoinPoint joinPoint) throws NoSuchMethodException{
        visitTime = new Date();
        clazz =joinPoint.getTarget().getClass();
        String name = joinPoint.getSignature().getName();
        Object[] args = joinPoint.getArgs();

        if (args == null || args.length == 0) {
            method=clazz.getMethod(name);
        }
        Class[] classArgs = new Class[args.length];
        for (int i = 0 ; i < args.length ; i++) {
            classArgs[i] = args[i].getClass();
        }
        clazz.getMethod(name,classArgs);

    }

    @After("execution(* com.wzw.web..*.*(..))")
    public void doAfter() {
        long time = new Date().getTime() - visitTime.getTime();

        String url = "";

        if (clazz != null && method != null && clazz != this.getClass()) {
            RequestMapping annotation = (RequestMapping)clazz.getAnnotation(RequestMapping.class);
            if (annotation != null) {
                String[] classValue = annotation.value();
                RequestMapping requestMappingAnnotation = method.getAnnotation(RequestMapping.class);
                if (requestMappingAnnotation != null) {
                    String[] methodValue = requestMappingAnnotation.value();
                    url = classValue[0] + methodValue[0];

                    String ip = request.getRemoteAddr();
                    logger.info("url = " +url+"       ip = "+ip+"   visitTime  =" +time+"ms");
                }
            }
        }

    }



}
```