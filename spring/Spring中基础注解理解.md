# Spring中基础注解理解

# 首先创建实体类

```java
public class Person{
    private Integer age;
    private String name;
}
```

## 配置文件

### 然后在配置文件中注册实体类

```xml
<bean id="person" class="xxx.Person">
    <property name="age" value="18"></property>
    <property name="name" value="zhangsan"></property>
</bean>
```

### main

```java
main{
    //根据xml配置文件获得容器
    //在容器中获得person类
}
```

![image-20211202123748889](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211202123748889.png)

## 配置类

### 如果使用注解的话，就不用配置文件，而改为配置类

```java
@Configuration // 告诉spring这是一个配置类，代替原来的配置文件
public class MainConfig{
    @Bean // 在容器中注册一个Bean，代替原来的<Bean>
    public Person person(){
        return new Person("lisi",30);
    }
}
```

### main

![image-20211202124059852](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211202124059852.png)

## @Value

配置文件中还用 value=“xxx”来设置默认值，在创建类时，也可以用@Value的方法来进行配置：

```java
public class Person{
    @Value("18")
    private Integer age;
    @Value("zhangsan")
    private String name;
}
```

