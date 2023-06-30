---
title: "一个注解实现SpringBoot接口防刷"
date: 2023-06-30T10:45:30+08:00
draft: false
tags: ['SpringBoot']
categories: ['实践笔记']
---

本文介绍一种极简洁、灵活通用接口防刷实现方式、通过在需要防刷的方法加上@Prevent 注解即可实现短信防刷；

使用方式大致如下：
```java
/**
 * 测试防刷
 *
 * @param request
 * @return
 */
@ResponseBody
@GetMapping(value = "/testPrevent")
@Prevent //加上该注解即可实现短信防刷(默认一分钟内不允许重复调用，支持扩展、配置）
public Response testPrevent(TestRequest request) {
    return Response.success("调用成功");
}
```

## 1、实现防刷切面PreventAop.java

大致逻辑为：定义一切面，通过@Prevent注解作为切入点、在该切面的前置通知获取该方法的所有入参并将其Base64编码，将入参Base64编码+完整方法名作为redis的key，入参作为reids的value，@Prevent的value作为redis的expire，存入redis；

每次进来这个切面根据入参Base64编码+完整方法名判断redis值是否存在，存在则拦截防刷，不存在则允许调用；

### 1.1 定义注解Prevent
```java
/**
 * 接口防刷注解
 * 使用：
 * 在相应需要防刷的方法上加上
 * 该注解，即可
 */
@Documented
@Target({ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
public @interface Prevent {

    /**
     * 限制的时间值（秒）
     *
     * @return
     */
    String value() default "60";

    /**
     * 提示
     */
    String message() default "";

    /**
     * 策略
     *
     * @return
     */
    PreventStrategy strategy() default PreventStrategy.DEFAULT;
}
```

### 1.2 实现防刷切面PreventAop
```java
/**
 * 防刷切面实现类
 */
@Aspect
@Component
public class PreventAop {
    private static Logger log = LoggerFactory.getLogger(PreventAop.class);

    @Autowired
    private RedisUtil redisUtil;


    /**
     * 切入点
     */
    @Pointcut("@annotation(com.zetting.aop.Prevent)")
    public void pointcut() {
    }


    /**
     * 处理前
     *
     * @return
     */
    @Before("pointcut()")
    public void joinPoint(JoinPoint joinPoint) throws Exception {
        String requestStr = JSON.toJSONString(joinPoint.getArgs()[0]);
        if (StringUtils.isEmpty(requestStr) || requestStr.equalsIgnoreCase("{}")) {
            throw new BusinessException("[防刷]入参不允许为空");
        }

        MethodSignature methodSignature = (MethodSignature) joinPoint.getSignature();
        Method method = joinPoint.getTarget().getClass().getMethod(methodSignature.getName(),
                methodSignature.getParameterTypes());

        Prevent preventAnnotation = method.getAnnotation(Prevent.class);
        String methodFullName = method.getDeclaringClass().getName() + method.getName();

        entrance(preventAnnotation, requestStr,methodFullName);
        return;
    }


    /**
     * 入口
     *
     * @param prevent
     * @param requestStr
     */
    private void entrance(Prevent prevent, String requestStr,String methodFullName) throws Exception {
        PreventStrategy strategy = prevent.strategy();
        switch (strategy) {
            case DEFAULT:
                defaultHandle(requestStr, prevent,methodFullName);
                break;
            default:
                throw new BusinessException("无效的策略");
        }
    }


    /**
     * 默认处理方式
     *
     * @param requestStr
     * @param prevent
     */
    private void defaultHandle(String requestStr, Prevent prevent,String methodFullName) throws Exception {
        String base64Str = toBase64String(requestStr);
        long expire = Long.parseLong(prevent.value());

        String resp = redisUtil.get(methodFullName+base64Str);
        if (StringUtils.isEmpty(resp)) {
            redisUtil.set(methodFullName+base64Str, requestStr, expire);
        } else {
            String message = !StringUtils.isEmpty(prevent.message()) ? prevent.message() :
                    expire + "秒内不允许重复请求";
            throw new BusinessException(message);
        }
    }


    /**
     * 对象转换为base64字符串
     *
     * @param obj 对象值
     * @return base64字符串
     */
    private String toBase64String(String obj) throws Exception {
        if (StringUtils.isEmpty(obj)) {
            return null;
        }
        Base64.Encoder encoder = Base64.getEncoder();
        byte[] bytes = obj.getBytes("UTF-8");
        return encoder.encodeToString(bytes);
    }
}
```
> 注：以上只展示核心代码、其他次要代码（例如redis配置、redis工具类等）可下载源码查阅

## 2、使用防刷切面

```java
/**
 * 切面实现入参校验
 */
@RestController
public class MyController {

    /**
     * 测试防刷
     *
     * @param request
     * @return
     */
    @ResponseBody
    @GetMapping(value = "/testPrevent")
    @Prevent
    public Response testPrevent(TestRequest request) {
        return Response.success("调用成功");
    }


    /**
     * 测试防刷
     *
     * @param request
     * @return
     */
    @ResponseBody
    @GetMapping(value = "/testPreventIncludeMessage")
    @Prevent(message = "10秒内不允许重复调多次", value = "10")//value 表示10表示10秒
    public Response testPreventIncludeMessage(TestRequest request) {
        return Response.success("调用成功");
    }
}
```

> gitee 源码：https://gitee.com/Zetting/my-gather/tree/master/springboot-aop-prevent