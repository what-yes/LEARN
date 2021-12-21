---
title: springboot+mybatisplus 查int型数据为null
date: 2021-11-19 12:30:10.35
updated: 2021-11-22 15:26:21.945
url: https://what-yes.top/archives/bugdatabase
categories: Bug
tags: Bug | 已解决  | springboot
---


## Bug
这两天有这样一个问题。
Mysql数据库有两张表，一张是User，一张是Account；

![image.png](https://oss.what-yes.top/halo-blog/image_1637295954074.png)

我用mybatisplus来进行crud操作。可是我发现查User表的时候一切正常，

![image.png](https://oss.what-yes.top/halo-blog/image_1637296015073.png)

但是查Account表时（或者说是除了User表以外的表），查int型数据最后都是null。

![image.png](https://oss.what-yes.top/halo-blog/image_1637296060771.png)

开始数据库在阿里云上，后来换到本地，都有这个问题。（但mybatisplus的代码没有改变）
这是pojo
```java
@Data
@AllArgsConstructor
@NoArgsConstructor
@ToString
@TableName("Account")
public class Account {

    @TableId(value = "account_id",type = IdType.AUTO)
    private Integer a_id;

    @TableField(value = "app_name")
    private String app;

    @TableField(value = "app_account")
    private String account;

    @TableField(value = "app_password")
    private String password;

    @TableField(value = "user_id")
    private Integer u_id;

}
```
```java
@Data
@AllArgsConstructor
@NoArgsConstructor
@ToString
@TableName("User")
public class User {

    @TableId(value = "user_id",type = IdType.AUTO)
    private Integer ID;

    @TableField(value="user_name")
    private String name;

    @TableField(value="user_password")
    private String password;

}
```
---
## 解决
功夫不负有心人啊，刚刚不死心又搜了一下在[这个连接](这个连接)里发现了，原来是我的实体类中int型变量中有`_`
![image.png](https://oss.what-yes.top/halo-blog/image_1637301682034.png)

刚刚改成**驼峰命名法**后就好了

![image.png](https://oss.what-yes.top/halo-blog/image_1637301729384.png)

**`_`不 行** 
**驼峰命名法yyds**











