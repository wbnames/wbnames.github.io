---
layout: post
title: 注解(Annotation)和自定义注解
category: Java
tags: [Java]
---

> java 注解，其实就是元数据，即一种描述数据的数据，和业务逻辑无关。

## 注解的作用

- 生成文档。也是java 最早提供的注解。常用的有@see @param @return 等
- 跟踪代码依赖性，实现替代配置文件功能。比较常见的是spring 2.5 开始的基于注解配置。作用就是减少配置。现在的框架基本都使用了这种配置来减少配置文件的数量
- 在编译时进行格式检查。如@Override放在方法前，如果你这个方法并不是覆盖了超类方法，则编译时就能检查出

## 注解的定义

比如在Spring中常用的Service注解定义如下：
```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component
public @interface Service {

  /**
   * The value may indicate a suggestion for a logical component name,
   * to be turned into a Spring bean in case of an autodetected component.
   * @return the suggested component name, if any
   */
  String value() default "";

}
```

## 自定义注解语法要求

- 使用@interface关键字自定义注解
- 成员以无参无异常方式声明
- 成员类型是受限的，合法的类型包括原始类型及String, Class, Annotation(注释), Enumeration(枚举)
- 可以用default为成员指定一个默认值
- 如果注解只有一个成员，则成员名必须取名为value()，在使用时可省略 成员名=
- 注解类可以没有成员，没有成员的注解为标识注解

## 四个元注解的作用：(元注解就是注解的注解)

- @Target 表示该注解的作用范围，可能的值在枚举类 ElemenetType 中，包括：  
    ElemenetType.CONSTRUCTOR    构造器（构造方法）声明  
    ElemenetType.FIELD    域（字段）声明（包括 enum 实例）  
    ElemenetType.LOCAL_VARIABLE    局部变量声明  
    ElemenetType.METHOD    方法声明  
    ElemenetType.PACKAGE    包声明  
    ElemenetType.PARAMETER    参数声明  
    ElemenetType.TYPE    类，接口（包括注解类型）或enum声明  
- @Retention 表示该注解的生命周期。可选的参数值在枚举类型 RetentionPolicy 中，包括：  
    RetentionPolicy.SOURCE    注解将被编译器丢弃，只在源码显示  
    RetentionPolicy.CLASS    注解在class文件中可用，但会被VM丢弃（运行时）  
    RetentionPolicy.RUNTIME   VM将在运行期也保留注释，因此可以通过反射机制读取注解的信息  
- @Inherited 允许子类继承父类中的注解  
- @Documented 将此注解包含在 javadoc 中 ，它代表着此注解会被javadoc工具提取成文档。在doc文档中的内容会因为此注解的信息内容不同而不同。相当与@see,@param 等。（可使用Export -> Javadoc方式生成Javadoc）

## 示例：自定义一个注解

```java
@Target({ElementType.METHOD,ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Inherited
@Documented
public @interface Description {

    String desc();

    String author();

    ing age() default 18;

}
```

### 注解的使用

```java
@Description(desc="I am eyeColor", author="Wayne Chu", age=18)
public String eyeColor(){
    rerurn "black";
}
```

### 注解的解析

原理：通过反射机制获取注解的信息
```java
public class ParseAnn {
    public static void main(String[] args) {
        try {
            // 使用类加载器加载类
            Class c = Class.forName("cn.waynechu.Person");
            // 找到类上面的注解
            boolean isExist = c.isAnnotationPresent(Description.class);
            // 上面的这个方法是用这个类来判断这个类是否存在Description这样的一个注解
            if (isExist) {
                // 拿到注解实例，解析类上面的注解
                Description d = (Description) c.getAnnotation(Description.class);
                System.out.println(d.value());
            }
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
    }
}
```
