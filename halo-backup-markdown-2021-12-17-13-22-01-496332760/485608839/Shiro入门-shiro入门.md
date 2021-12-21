---
title: Shiro入门
date: 2021-11-27 11:24:52.439
updated: 2021-11-27 11:30:37.539
url: https://what-yes.top/archives/shiro入门
categories: 
tags: springboot | shiro
---

# Shiro

## 介绍

shiro 是一款主流的Java安全框架，不依赖任何容器，可以运行在Jave SE 和Java EE项目中，它的主要作用是对访问系统的用户进行身份认证、授权、会话管理、加密等操作。

## Shiro 核心组件

### 用户 角色 权限 

会给角色赋予权限，给用户赋予角色。![20211126192950](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/20211126192950.png)



比如角色1是副总经理，角色2是总经理。用户是副总经理时有两个权限，当希望他有三个权限（或者更多权限）时，只需要让他的角色变成总经理就行，更加灵活。



![image-20211126204339436](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211126204339436.png)

### UsernamePasswordToken

Shiro 用来封装用户登录信息，使用用户的登录信息来创建令牌`Token`。

### SecurityManager 

Shiro 的核心部分，负责安全认证和授权。

### Subject

Shiro 的一个抽象概念，包含了用户信息。

### Realm

开发者自定义的模块，根据项目的需求，验证和授权的逻辑全部写在`Realm`中。

### AuthenticationInfo

用户的角色信息集合，认证时使用。

### AuthorizationInfo

用户的权限信息集合，授权时使用。

### DefaultWebSecurityManager

安全管理器，开发者自定义的Realm需要注入到这里才有效。

### ShiroFilterFactoryBean

过滤器工厂，Shiro的基本运行机制是开发者自定义机制，Shiro去执行，具体的执行操作就是由`ShiroFilterFactoryBean`创建的 Filter对象来完成。

### 关系图

![image-20211126205309367](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211126205309367.png)

## 具体实现

### 1. 目标

![image-20211126200214441](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211126200214441.png)

### 2. maven仓库

![image-20211126193950244](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211126193950244.png)

### 3. 数据库 

数据库用户、权限、角色本来应该是三张表，这里为了简便放到一起了。

 ![image-20211126203646616](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211126203646616.png)

### 4. 流程（我知道这不是流程图）

![Shiro2](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/Shiro2.png)

<a name="order"></a>

![image-20211126195714251](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211126195714251.png)

### 5. <a name="realm"></a>在Realm中设置了认证`doGetAuthenticationInfo()`和授权`doGetAuthorizationInfo()`的相关操作

```java
public class UserRealm extends AuthorizingRealm {

    @Autowired
    userService userService;

    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        Subject subject = SecurityUtils.getSubject();

        // SimpleAuthenticationInfo(user, user.getPassword(),getName());第一个参数已经把user传进去了
        // SimpleAuthenticationInfo的第一个参数就是 principal
        User user = (User) subject.getPrincipal();

        // 设置角色
        Set<String> roles = new HashSet<>();
        roles.add(user.getRole());
        SimpleAuthorizationInfo info = new SimpleAuthorizationInfo(roles);

        // 设置权限
        info.addStringPermission(user.getPerms());
        return info;
    }

    // 认证
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        UsernamePasswordToken token = (UsernamePasswordToken) authenticationToken;
        User user = userService.findByusername(token.getUsername());
        if(user != null){
            // 第一个参数：传入的都是com.java.entity包下的User类的user对象。
            // 注意：此参数可以通过subject.getPrincipal()方法获取—获取当前记录的用户，从这个用户对象进而再获取一系列的所需要的属性。
            // 传入的是从数据库中获取到的password，然后再与token中的password进行对比，匹配上了就通过，匹配不上就报异常。
            return new SimpleAuthenticationInfo(user, user.getPassword(),getName());
        }
        return null;
    }
}
```



<a name="unauth"></a>

### 6. 将`Realm`注册到`SecurityManager`中，将`SecurityManager`注册到`shiroFilterFactoryBean`中。<a href="#order">顺序参考</a>



```java
@Configuration
public class ShiroConfig {

    @Bean
    public ShiroFilterFactoryBean shiroFilterFactoryBean(@Qualifier("securityManager") DefaultWebSecurityManager securityManager){
        ShiroFilterFactoryBean factoryBean = new ShiroFilterFactoryBean();
        factoryBean.setSecurityManager(securityManager);

        //权限设置
        Map<String, String> map = new Hashtable<>();
        // 访问 /main 需要登录
        map.put("/main","authc");
        // 访问 /manage 需要有manage权限
        map.put("/manage","perms[manage]");
        map.put("/admin","roles[admin]");
        factoryBean.setFilterChainDefinitionMap(map);

        // 设置默认登录页面，即当用户未登录且访问需要登录的东西时，则需要跳转到这个页面
        factoryBean.setLoginUrl("/login");
        
        // 设置未授权
        factoryBean.setUnauthorizedUrl("/unauth");
        return factoryBean;
    }

    @Bean
    public DefaultWebSecurityManager securityManager(@Qualifier("userRealm") UserRealm userRealm){
        DefaultWebSecurityManager manager = new DefaultWebSecurityManager();
        manager.setRealm(userRealm);
        return manager;
    }

    @Bean
    public UserRealm userRealm(){
        return new UserRealm();
    }
}
```



### 7. 用户发送访问需要登录的页面时自动跳到登录页面，输入账号密码并请求后会根据账号密码生成`Token`，通过`subject`的`login(Token)`方法进行登录。



```java
// 设置默认登录页面，即当用户未登录且访问需要登录的东西时，则需要跳转到这个页面
factoryBean.setLoginUrl("/login");
```

```java
@PostMapping("/login")
public String login(String name, String password){
    UsernamePasswordToken token =
        new UsernamePasswordToken(name, password);
    Subject subject = SecurityUtils.getSubject();
    subject.login(token);
    return "index";
}
```



### 8. `subject`的`login(Token)`方法会跳到<a href="#realm">Realm</a>中进行相关的验证。

#### 账号密码都正确就可以进入

![image-20211127110341263](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211127110341263.png)

#### 否则会报对应异常

##### 未知账号异常

`UnknownAccountException`

##### 密码错误异常

`IncorrectCredentialsException`

```java
// login()
try {
    subject.login(token);
    return "redirect:/index";
} catch (UnknownAccountException e) {
    model.addAttribute("msg", "账号不存在");
    e.printStackTrace();
    return "login";
}catch (IncorrectCredentialsException e){
    model.addAttribute("msg","密码错误");
    return "login";
}
```

### 9. 访问未认证页面

进入页面后，一般会根据不同权限有不同界面，比如对用一般用户而言就不会有管理相关的按钮选项，但回了防止用户知道管理页面的`url`链接直接访问，那么对于一般用户而言由于没有权限会直接返回一个错误页面（错误页面的url是`xxx/unauth.html`,在<a href="#unauth">这里</a>设置的），造成不好的用户体验，所以需要对应的处理页面。为了简便就直接用`@ResponseBody`



```java
@GetMapping("unauth")
@ResponseBody
public String unauth(){
    return "未授权，无法访问";
}
```

![image-20211127105711605](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211127105711605.png)

## 其它一些规则介绍

就是<a href="#unauth">这个地方</a>设置的东西。

### 认证过滤器：

- anon: 无需认证
- authc：必须认证
- authBase：必须通过HTTPBasic人则会那个
- user：不一定通过认证，只要曾经被shiro记录即可，比如：记住我。

### 授权过滤器：

- perms：必须拥有某个权限才能访问
- role：必须拥有某个角色才能访问
- port：请求的端口必须是指定值才可以
- rest：请i去必须基于RESTful,POST，PUT，GET，DELETE
- ssl：必须是安全的URL请求，协议HTTPS

## 其它

### 题外话：mapper service controller各自的功能（调用关系）

service 中的函数调用mapper的函数实现相应功能，controller中调用service中的函数



