







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

### 15.2 Spring类型转换各自的使用场景

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

### 15.3 JavaBeans接口的类型转换：Spring是如何扩展PropertyEditor接口实现类型的转换

- 核心职责

  - 将String类型的内容转化为目标类型的对象

  

- 扩展原理
  - Spring框架将文本内容传递到PropertyEditor实现的setAsText(String)方法
  - PropertyEditor#setAsText(String)方法实现将String类型转化为目标类型的对象
  - 将目标类型的对象传入PropertyEditor#setValue(Object)方法
  - PropertyEditor#setValue(Object)方法实现需要临时存储传入对象
  - Spring框架将通过PropertyEditor#getValue()获取类型转换后的对象





### 15.4 Spring3 通用类型转换接口

- 普通类型转换接口 - org.springframework.core.convert.converter.Converter<S , T>

  - ```java
    @Nullable
    T convert(S source);
    ```

- 通用类型接口转换 - org.springframework.core.convert.converter.GenericConverter

  - ```java
    Set<ConvertiblePair> getConvertibleTypes();
    ```

  - ```java
    Object convert(@Nullable Object source, TypeDescriptor sourceType, TypeDescriptor targetType);
    ```



> 1、GenericConverter通用类型转换接口，支持多类型转化。通常具有更加好的使用范围。
>
> 2、Converter 单一类型转换





### 15.5 Spring内建类型转换器

- **内建扩展**

  | 转换场景            | 实现类所在报名（package）                    |
  | ------------------- | -------------------------------------------- |
  | 日期/时间相关       | org.springframework.format.datetime          |
  | java8 日期/时间相关 | org.springframework.format.datetime.standard |
  | 通用实现            | org.springframework.core.convert.support     |

  

### 15.6 Spring GenericConverter比Converter接口更加通用？





### 十六章 Spring 泛型





### 十七章 Spring事件



#### 17.1 java事件/监听器编程模型

- java.util.Observable - 观察者源，（被观察的对象）

  ```java
  package java.util;
  public class Observable {
      private boolean changed = false;
      private Vector<Observer> obs;
      /** Construct an Observable with zero Observers. */
      public Observable() {
          obs = new Vector<>();
      }
      public synchronized void addObserver(Observer o) {
          if (o == null)
              throw new NullPointerException();
          if (!obs.contains(o)) {
              obs.addElement(o);
          }
      }
      public synchronized void deleteObserver(Observer o) {
          obs.removeElement(o);
      }
      public void notifyObservers() {
          notifyObservers(null);
      }
      public void notifyObservers(Object arg) {
        
          Object[] arrLocal;
  
          synchronized (this) {
              if (!changed)
                  return;
              arrLocal = obs.toArray();
              clearChanged();
          }
  
          for (int i = arrLocal.length-1; i>=0; i--)
              ((Observer)arrLocal[i]).update(this, arg);
      }
      public synchronized void deleteObservers() {
          obs.removeAllElements();
      }
      protected synchronized void setChanged() {
          changed = true;
      }
      protected synchronized void clearChanged() {
          changed = false;
      }
      public synchronized boolean hasChanged() {
          return changed;
      }
      public synchronized int countObservers() {
          return obs.size();
      }
  }
  
  ```

- java.util.Observer - 观察者，java默认为接口

  ```java
  package java.util;
  
  public interface Observer {
      /**
       * This method is called whenever the observed object is changed. An
       * application calls an <tt>Observable</tt> object's
       * <code>notifyObservers</code> method to have all the object's
       * observers notified of the change.
       *
       * @param   o     the observable object.
       * @param   arg   an argument passed to the <code>notifyObservers</code>
       *                 method.
       */
      void update(Observable o, Object arg);
  }
  
  ```

- java.util.EventObject - 事件源

- java.util.EventListener - 事件监听者（没有任何实现的接口，作用：标识实现类为事件监听者）

### 17.2 面向接口的事件/监听器设计模式

- 事件/监听场景举例

  | java技术规范    | 事件接口                              | 监听接口                                 |
  | --------------- | ------------------------------------- | ---------------------------------------- |
  | JavaBeans       | java.beans.PropertyChangeEvent        | java.beans.PropertyChangeListener        |
  | Java AWT        | java.awt.event.MouseEvent             | java.awt.event.MouseListener             |
  | Java Swing      | javax.swing.event.MenuEvent           | javax.swing.event.MenuListener           |
  | Java Preference | java.util.prefs.PreferenceChangeEvent | java.util.prefs.PreferenceChangeListener |

  

### 17.3  面向注解的事件/监听器设计模式





### 17.4 Spring标准事件 ApplicationEvent

- org.springframework.context.ApplicationEvent

  ```java
  package org.springframework.context;
  
  import java.util.EventObject;
  public abstract class ApplicationEvent extends EventObject {
  
  	/** use serialVersionUID from Spring 1.2 for interoperability */
  	private static final long serialVersionUID = 7099057708183571937L;
  
  	/** System time when the event happened */
  	private final long timestamp;
  
  	public ApplicationEvent(Object source) {
  		super(source);
  		this.timestamp = System.currentTimeMillis();
  	}
  
  	public final long getTimestamp() {
  		return this.timestamp;
  	}
  }
  ```

  相对于java的EventObject 而言 ApplicationEvent 只增加了`timestamp`事件发生时间



### 17.5 基于注解的Spring事件监听器

- org.springframework.context.event.EventListener

  | 特性                 | 说明                                     |
  | -------------------- | ---------------------------------------- |
  | 设计特点             | 支持多ApplicationEvent类型，无需接口约束 |
  | 注解目标             | 方法                                     |
  | 是否支持异步执行     | 支持                                     |
  | 是否支持泛型类型事件 | 支持                                     |
  | 是否支持顺序控制     | 支持，配合@Order注解控制                 |

  

### 17.6 Spring 事件发布器

- 方法一：通过 org.springframework.context.ApplicationEventPublisher 发布Spring事件（事件发布）
  - 获取ApplicationEventPublisher
    - 依赖注入
- 方法二：通过 org.springframework.context.event.ApplicationEventMulticaster 发布Spring事件 （事件广播）
  - 获取ApplicationEventMulticaster 
    - 依赖注入
    - 依赖查找



### 17.7 Spring 层次性上下文事件传播

- 发生说明

  当Spring应用出现多层次Spring应用上下文（ApplicationContext）时，如Spring WebMVC 、Spring Boot或Spring Cloud场景下，由子ApplicationContext发起Spring事件可能会传递到其Parent ApplicatoinContext（知道Root）的过程

  

- 如何避免

  - 定位Spring事件源（ApplicationContex）进行过滤处理



































































