







##    InstantiationAwareBeanPostProcessor接口







## Spring Bean Aware 接口回调

> 顺序性

第一调用栈（ApplicationContextAwareProcessor）

- BeanNameAware
- BeanClassLoaderAware

- BeanFactoryAware

第二调用栈

- EnvironmentAware
- EmbeddedValueResolverAware
- ResourceLoaderAware
- ApplicationEventPublisherAware
- 



## 十二章 12 Spring国际化（i18n）

Spring国际化应用场景

- 普通国际化文案

- Bean Validation  校验国际化文案

- Web站点页面渲染

- Web MVC错误消息提示

### 12.1 Spring国际化接口

- 核心接口

  - org.springframework.context.MessageSource

  ```
  org.springframework.context.support.ResourceBundleMessageSource
  org.springframework.context.support.ReloadableResourceBundleMessageSource
  ```

- 主要概念

  - 文案模板编码（code)
  - 文案模板参数（args）
  - 区域（Locale）



### 12.2 层次性MessageSource

- Spring层次性接口

  - org.springframework.beans.factory.HierarchicalBeanFactory
  - org.springframework.context.ApplicationContext
  - org.springframework.beans.factory.config.BeanDefinition

- MessageSource 层次性结构体现

  - org.springframework.context.HierarchicalMessageSource

  

### 12.3 Java国际化标准实现

- 核心接口
  - 抽象实现 - java.util.ResourceBundle
  - Properties资源实现 -java.util.PropertyResourceBundle
  - 举例实现  -java.util.ListResourceBundle（硬编码实现，hard code）
- ResourceBundle核心特性
  - Key-Value 设计
  - 层次新设计
  - 缓存设计
  - 字符编码控制 - java.util.ResourceBundle.Control  （@Since 1.6）
  -  Control SPI扩展 -ResourceBundleControlProvider （@Since 1.8）

### 12.4 Java文本格式化

- 核心接口

  - MessageFormat

    | FormatType         | FormatStyle                                                  | Subformat Created                                            |
    | ------------------ | :----------------------------------------------------------- | ------------------------------------------------------------ |
    | *(none)*           | *(none)*                                                     | `null`                                                       |
    | `number`           | *(none)*                                                     | [`NumberFormat.getInstance`](https://docs.oracle.com/javase/8/docs/api/java/text/NumberFormat.html#getInstance-java.util.Locale-)`(getLocale())` |
    | `integer`          | [`NumberFormat.getIntegerInstance`](https://docs.oracle.com/javase/8/docs/api/java/text/NumberFormat.html#getIntegerInstance-java.util.Locale-)`(getLocale())` |                                                              |
    | `currency`         | [`NumberFormat.getCurrencyInstance`](https://docs.oracle.com/javase/8/docs/api/java/text/NumberFormat.html#getCurrencyInstance-java.util.Locale-)`(getLocale())` |                                                              |
    | `percent`          | [`NumberFormat.getPercentInstance`](https://docs.oracle.com/javase/8/docs/api/java/text/NumberFormat.html#getPercentInstance-java.util.Locale-)`(getLocale())` |                                                              |
    | *SubformatPattern* | `new` [`DecimalFormat`](https://docs.oracle.com/javase/8/docs/api/java/text/DecimalFormat.html#DecimalFormat-java.lang.String-java.text.DecimalFormatSymbols-)`(subformatPattern,` [`DecimalFormatSymbols.getInstance`](https://docs.oracle.com/javase/8/docs/api/java/text/DecimalFormatSymbols.html#getInstance-java.util.Locale-)`(getLocale()))` |                                                              |
    | `date`             | *(none)*                                                     | [`DateFormat.getDateInstance`](https://docs.oracle.com/javase/8/docs/api/java/text/DateFormat.html#getDateInstance-int-java.util.Locale-)`(`[`DateFormat.DEFAULT`](https://docs.oracle.com/javase/8/docs/api/java/text/DateFormat.html#DEFAULT)`, getLocale())` |
    | `short`            | [`DateFormat.getDateInstance`](https://docs.oracle.com/javase/8/docs/api/java/text/DateFormat.html#getDateInstance-int-java.util.Locale-)`(`[`DateFormat.SHORT`](https://docs.oracle.com/javase/8/docs/api/java/text/DateFormat.html#SHORT)`, getLocale())` |                                                              |
    | `medium`           | [`DateFormat.getDateInstance`](https://docs.oracle.com/javase/8/docs/api/java/text/DateFormat.html#getDateInstance-int-java.util.Locale-)`(`[`DateFormat.DEFAULT`](https://docs.oracle.com/javase/8/docs/api/java/text/DateFormat.html#DEFAULT)`, getLocale())` |                                                              |
    | `long`             | [`DateFormat.getDateInstance`](https://docs.oracle.com/javase/8/docs/api/java/text/DateFormat.html#getDateInstance-int-java.util.Locale-)`(`[`DateFormat.LONG`](https://docs.oracle.com/javase/8/docs/api/java/text/DateFormat.html#LONG)`, getLocale())` |                                                              |
    | `full`             | [`DateFormat.getDateInstance`](https://docs.oracle.com/javase/8/docs/api/java/text/DateFormat.html#getDateInstance-int-java.util.Locale-)`(`[`DateFormat.FULL`](https://docs.oracle.com/javase/8/docs/api/java/text/DateFormat.html#FULL)`, getLocale())` |                                                              |
    | *SubformatPattern* | `new` [`SimpleDateFormat`](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html#SimpleDateFormat-java.lang.String-java.util.Locale-)`(subformatPattern, getLocale())` |                                                              |
    | `time`             | *(none)*                                                     | [`DateFormat.getTimeInstance`](https://docs.oracle.com/javase/8/docs/api/java/text/DateFormat.html#getTimeInstance-int-java.util.Locale-)`(`[`DateFormat.DEFAULT`](https://docs.oracle.com/javase/8/docs/api/java/text/DateFormat.html#DEFAULT)`, getLocale())` |
    | `short`            | [`DateFormat.getTimeInstance`](https://docs.oracle.com/javase/8/docs/api/java/text/DateFormat.html#getTimeInstance-int-java.util.Locale-)`(`[`DateFormat.SHORT`](https://docs.oracle.com/javase/8/docs/api/java/text/DateFormat.html#SHORT)`, getLocale())` |                                                              |
    | `medium`           | [`DateFormat.getTimeInstance`](https://docs.oracle.com/javase/8/docs/api/java/text/DateFormat.html#getTimeInstance-int-java.util.Locale-)`(`[`DateFormat.DEFAULT`](https://docs.oracle.com/javase/8/docs/api/java/text/DateFormat.html#DEFAULT)`, getLocale())` |                                                              |
    | `long`             | [`DateFormat.getTimeInstance`](https://docs.oracle.com/javase/8/docs/api/java/text/DateFormat.html#getTimeInstance-int-java.util.Locale-)`(`[`DateFormat.LONG`](https://docs.oracle.com/javase/8/docs/api/java/text/DateFormat.html#LONG)`, getLocale())` |                                                              |
    | `full`             | [`DateFormat.getTimeInstance`](https://docs.oracle.com/javase/8/docs/api/java/text/DateFormat.html#getTimeInstance-int-java.util.Locale-)`(`[`DateFormat.FULL`](https://docs.oracle.com/javase/8/docs/api/java/text/DateFormat.html#FULL)`, getLocale())` |                                                              |
    | *SubformatPattern* | `new` [`SimpleDateFormat`](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html#SimpleDateFormat-java.lang.String-java.util.Locale-)`(subformatPattern, getLocale())` |                                                              |
    | `choice`           | *SubformatPattern*                                           | `new` [`ChoiceFormat`](https://docs.oracle.com/javase/8/docs/api/java/text/ChoiceFormat.html#ChoiceFormat-java.lang.String-)`(subformatPattern)` |

  

- **Usage Information**

  > ```
  > int planet = 7;
  >  String event = "a disturbance in the Force";
  > 
  >  String result = MessageFormat.format(
  >      "At {1,time} on {1,date}, there was {2} on planet {0,number,integer}.",
  >      planet, new Date(), event);
  >  
  > ```

  The output is:

  > ```
  >  At 12:30 PM on Jul 3, 2053, there was a disturbance in the Force on planet 7.
  > ```



### 12.5 MessageSource开箱即用实现

spring开箱即用的接口

- Resourcebundle + MessageSource
  - org.springframework.context.support.ResourceBundleMessageSource
- org.springframework.context.support.ReloadableResourceBundleMessageSource

### 12.6 MessageSource内建实现

- org.springframework.context.support.ResourceBundleMessageSource
- org.springframework.context.support.ReloadableResourceBundleMessageSource
- org.springframework.context.support.StaticMessageSource
- org.springframework.context.support.DelegatingMessageSource

### 12.7 MessageSource应用

动态更新MessageSource.



## 十三章 Spring校验(Validation)

### 13.1 Spring校验使用场景：为什么Validator不只是Bean的校验

### 13.2 Validator接口设计

### 13.3 Errors接口设计

### 13.4 Errors文案来源

### 13.5 自定义Validator

## 十四章 数据绑定（Data Binding）

### 14.1 数据绑定使用场景

### 14.2 数据绑定组件 DataBinder

### 14.3 DataBinder绑定元数据：PropertyValues不是Spring Bean的原信息吗？

### 14.4 DataBinder绑定控制参数

### 14.5 Spring BeanWrapper源于JavaBeans（Introspector 、 BeanInfo）

### 14.6 BeanWrapper使用场景

### 14.7 JavaBeans是如何操作属性的

### 14.8 DataBinder数据校验:又见Validator









## 十五章 Spring类型转换 （Type Conversion）

### 15.1 Spring类型转换的实现

- Overview
  1. Spring类型转换的实现
  2. 使用场景
  3. 基于JavaBeans接口的类型转换
  4. Spring内建PropertyEditor扩展
  5. 自定义PropertyEditor扩展
  6. Spring PropertyEditor 的设计缺陷
  7. Spring3 通用类型转换接口
  8. Spring内建类型转换器
  9. Converter接口的局限性
  10. GenericConverter接口‘
  11. 优化GenericConverter接口
  12. 扩展Spring类型转换器
  13. 统一类型转换服务
  14. ConversionService作为依赖

### 15.2 

- 基于JavaBeans接口的类型转换实现
  - 基于java.beans.PropertyEditor
- Spring 3.0+ 通用类型转换实现
- 场景分析

| 场景               | 基于JavaBeans接口的类型转换实现 | Spring 3.0+ 通用类型转换实现 |
| ------------------ | ------------------------------- | ---------------------------- |
| 数据绑定           | YES                             | YES                          |
| BeanWrapper        | YES                             | YES                          |
| Bean属性类型转换   | YES                             | YES                          |
| 外部化属性类型转换 | NO                              | YES                          |
|                    |                                 |                              |















