---
title: Mybatis工作流程浅析
categories:
  - java
copyright: true
date: 2018-12-31 15:36:35
tags: [java,mybatis]
top: true
password:
---

# Mybatis核心流程三大阶段

1. 初始化阶段
   读取XML配置文件和注解中的配置信息，创建配置对象，并完成各个模块的初始化工作

2. 代理阶段

   封装iBatis的编程模型，使用Mapper接口开发的初始化工作

3. 数据读写阶段

   通过SqlSession完成SQL的解析，参数的映射，SQL的执行，结果的反射解析过程

   <!-- more -->

## 1.初始化阶段

读取XML配置文件和数据库配置文件中的信息加载到configuration对象中；

![mybatis初始化configuration对象](..\imgs\Mybatis-source-process-1.JPG)

初始化流程：

1. 读取xml配置文件加载到内存。configuration对象创建于SqlSessionFactory对象中，该对象的属性在SqlSessionFactory中读取xml文件并赋值。

2. 工厂类生产SqlSession。在SqlSessionFactory对象中，通过openSession方法return一个SqlSession对象。


## 2.代理阶段

流程为`配置文件解读 + 动态代理的增强`，即

- 找到session中对应的方法执行；
- 找到命名空间和方法名
- 传递参数

---

解读SqlSession会话

> SqlSession意味着创建数据库会话，代表一次与数据库的连接；
>
> - 是Mybatis对外提供数据访问的主要API（来自于ibatis编程的方式）；
> - 实际上SqlSession的功能都是基于Executor来实现的；

在SqlSession类中声名configuration和Executor对象，然后最终所有SQL语句调用Executor去执行。

例如：

```java
//1.对外提供服务，把请求转发给Executor
//2.给mapper接口生成实现类
public class SqlSession {
	private Configuration conf;
    private Executor executor;
    
    public SqlSession(Configuration conf) {
        this.conf = conf;
        executor = new Executor();
    }
    //该方法是iBatis编程模型方法，statement为mapper.xml的命名空间+类名的合成作为参数
    public <T> T selectOne(String statement, Object parameter) {
        MappedStatment ms = conf.getMappedStatement().get(statement);
        //ms为传入的SQL语句，parameter为查询参数
        return executor.query(ms, parameter);
    }
    
    //使用mapper接口编程，利用动态代理和反射机制
    public <T> T getMapper(Class<T> type) {
        MappedProxy mp = new MappedProxy(this);//this标识SqlSession本身
        //参数分别为1.类加载器，2.类型数组，3.反射机制找到的sql语句mp
        return (T) Proxy.newProxyInstance(type.getClassLoader, new Class[]{type}, mp);
    }
}
```

> mybatis两种编程模型
>
> - Mybatis封装了iBatis编程模型，使用SqlSession对外提供数据库的访问；
> - 使用Mapper接口编程，就可以访问数据库。

## 3.数据读写阶段

即Executor的执行，需要遵循JDBC规范。

注：Executor执行后的结果需要通过反射机制确定结果类型。

---

## 总体的实现思路

> 查询语句的实现流程如下：
>
> ---
>
> ​	***一阶段***
>
> 1. 创建SqlSessionFactory实例；
>
> 2. 实例化过程中，加载配置文件创建configuration对象；
>
> 3. 通过factory创建SqlSession；
>
>    ---------------------------------------------
>
>    ***二阶段***
>
> 4. 通过SqlSession获取mapper接口动态代理；
>
> 5. 动态代理回调SqlSession中某个查询方法；
>
>    -------------------------------------
>
>    ***三阶段***
>
> 6. SqlSession将查询结果转发给Executor；
>
> 7. Executor基于JDBC访问数据库获取数据；
>
> 8. Executor通过反射将数据转化成POJO并返回给SqlSession；
>
> 9. 将数据返回给调用者。