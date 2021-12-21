---
title: Springboot + MybatisPlus入门
date: 2021-11-22 15:09:47.092
updated: 2021-11-26 13:57:10.478
url: https://what-yes.top/archives/mybatisplusrumen
categories: 
tags: springboot
---

# Springboot + MybatisPlus入门
## 项目结构

![image-20211122151253315](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211122151253315.png)
## 数据库

![image-20211122144638441](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211122144638441.png)

## 导入相关依赖

在 `IDEA`用 `SpringbootInitializer`创建好项目后，`在pom.xml`中额外加入如下依赖

```xml
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.4.3.4</version>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <scope>runtime</scope>
</dependency>
```

## 修改配置文件

`application.properties`

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/test?serverTimezone=GMT%2B8
spring.datasource.username=root
spring.datasource.password=xxxxx
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
mybatis-plus.mapper-locations= classpath:mapper/*.xml
```

## 创建pojo

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
@ToString
@TableName("user")
public class User {

    @TableId(value = "id")
    private Integer ID;

    @TableField(value = "name")
    private String Name;

    private String sex;
}
```

注解会有专门一篇博客来解释，这里就不多说了。有兴趣可以去查一查，查了之后配合注解名字也很好理解。

## mapper文件

```
结构：
----mapper
	----userMapper.java
```

```java
public interface userMapper extends BaseMapper<User> {
}
```

## service相关文件

其实如果格式不要求那么标准的话，可以直接用`mapper` 进行 `crud`操作。

但是如果为了项目结构的标准，还是需要 `service`

```
结构
----service
	----impl
		----userServiceImpl.java
	----userService.java
```

```java
// userService.java
public interface userService extends IService<User> {
}
```

```java
// userServiceImpl.java
@Service
public class userServiceImpl extends ServiceImpl<userMapper, User> implements userService {
}
```

## 测试

### 直接用mapper

如果直接用 `mapper`，要在 `userMapper`中加入 `@Mapper`的注解

```java
@Autowired
userMapper userMapper;
@Test
void testMapper(){
    List<User> users = userMapper.selectList(null);
    users.forEach(System.out::println);
}
```

### 用Service

[条件构造器](https://baomidou.com/guide/wrapper.html)

```java
    @Autowired
    userService userService;


    @Test
	// 查询所有
	// 根据ID（主键）查询
    void listAllUser(){
        List<User> list = userService.list();
        list.forEach(System.out::println);
        System.out.println(userService.getById(1));
    }

	// 根据自己定义的一些规则查询
    @Test
    void selectQuery(){
        QueryWrapper<User> userQueryWrapper = new QueryWrapper<>();
        userQueryWrapper.eq("sex", 1);
        List<User> list = userService.list(userQueryWrapper);
        list.forEach(System.out::println);
    }

```

### 运行结果

![image-20211122150756013](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211122150756013.png)

## 后话

新手可能会很好奇：

1. 为什么一点 `sql`语句也没写，自己也没实现任何接口，但调用相关方法却可以起作用？
2. 调用逻辑是怎样的？

目前我只知道如果你学过 `Mybatis`那么你会理解调用逻辑。至于第一个问题，我还得好好看看源码才知道。后续应该也会记录我的一些理解。 

## 分页查询

### 添加 `mybatisplus`配置类

```java
@Configuration
public class MybatisPlusConfig {
    @Bean
    public MybatisPlusInterceptor mybatisPlusInterceptor() {
        MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
        interceptor.addInnerInterceptor(new PaginationInnerInterceptor(DbType.MYSQL));
        return interceptor;
    }
}
```

### 测试

`Page<User> userPage = new Page<>(1,2);` 每页两条信息，查询第一页

```java
@Autowired
userMapper userMapper;

@Test
void testPageSelect(){
    Page<User> userPage = new Page<>(1,2);
    IPage<User> userIPage = userMapper.selectPage(userPage, null);
    System.out.println("总页数:" + userIPage.getPages());
    System.out.println("总记录数： "+userIPage.getTotal());
    userIPage.getRecords().forEach(System.out::println);
}
```

### 结果：

本来就只有3条数据，设置的是每页两条查询第一页，所以就是如下数据。



![image-20211126135215177](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211126135215177.png)



这只是很基础的用法，详细的用户可以参照[官网](https://baomidou.com/guide/page.html)或是理解源码自己摸索。

