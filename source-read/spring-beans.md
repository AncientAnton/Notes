# Spring Framework

* 应用上下文
    * 读取元数据
    * 提供BeanFactory容器
        * Bean的实例化与初始化
        * 管理Bean的依赖
        * 管理Bean的作用域
        * 管理Bean的生命周期
    * 提供Environment
    * 提供LoadTimeWeaver支持
    * 提供文本国际化支持
    * 提供应用事件支持
* 资源访问
    * Resource
    * ResourceLoader
    * ResourceLoaderAware
* 校验
* 数据绑定
* 类型转换
* Spring表达式
* AOP切面编程API
* 统一抽象的Buffer

# 示例

创建TestBean类

```
public class TestBean {

    public void test() {
        System.out.println("test");
    }
}
```

创建配置文件`beans.xml`

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="testBean" class="beans.TestBean">

    </bean>
</beans>
```

使用应用上下文

```
ApplicationContext applicationContext = new ClassPathXmlApplicationContext("beans.xml");
TestBean testBean = applicationContext.getBean("testBean", TestBean.class);
testBean.test();
```

# 分析

逐步分析架构

## 1. ApplicationContext

应用上下文的抽象，除了继承BeanFactory容器相关接口，还有其它功能接口

```
public interface ApplicationContext extends EnvironmentCapable, ListableBeanFactory, HierarchicalBeanFactory,
    MessageSource, ApplicationEventPublisher, ResourcePatternResolver {

  /**
   * 返回应用上下文的ID
   */
  String getId();

  /**
   * 返回应用的名称
   */
  String getApplicationName();

  /**
   * 返回应用上下文的名称
   */
  String getDisplayName();

  /**
   * 返回第一次加载时间戳
   */
  long getStartupDate();

  /**
   * 返回父应用上下文
   */
  ApplicationContext getParent();

  /**
   * 获取应用上下文中的BeanFactory容器
   */
  AutowireCapableBeanFactory getAutowireCapableBeanFactory() throws IllegalStateException;

}
```

# 1.1 EnvironmentCapable 

提供环境抽象

```
public interface EnvironmentCapable {

  /**
   * 返回对应的Environment
   */
  Environment getEnvironment();

}
```

### 1.1.1 Environment

环境提供了多套配置文件的抽象，另外包括属性值的读取，以及占位符的支持

```
public interface Environment extends PropertyResolver {

  /**
   * 返回当前活跃的配置文件 spring.profiles.active配置
   */
  String[] getActiveProfiles();

  /**
   * 返回默认的配置文件 spring.profiles.default配置
   */
  String[] getDefaultProfiles();

  /**
   * 判断是否包含活跃配置
   * 可以使用 ! 代表取反
   * env.acceptsProfiles("p1", "!p2")
   */
  boolean acceptsProfiles(String... profiles);

}
```

### 1.1.2 PropertyResolver

获取对应属性值，解析`${...}`占位符

```
public interface PropertyResolver {

	/**
	 * 是否包含属性
	 */
	boolean containsProperty(String key);

	/**
	 * 返回对应属性值
	 */
	String getProperty(String key);

	/**
	 * 返回带有默认值的属性
	 */
	String getProperty(String key, String defaultValue);

	/**
	 * 返回目标类型的属性
	 */
	<T> T getProperty(String key, Class<T> targetType);

	/**
	 * 返回目标类型的属性，不存在则返回默认值
	 */
	<T> T getProperty(String key, Class<T> targetType, T defaultValue);

	/**
	 * 没有默认值则抛出异常
	 */
	String getRequiredProperty(String key) throws IllegalStateException;

	/**
	 * 没有默认值则抛出异常
	 */
	<T> T getRequiredProperty(String key, Class<T> targetType) throws IllegalStateException;

	/**
	 * 解析 ${...} 占位符，使用getProperty代替
	 */
	String resolvePlaceholders(String text);

	/**
	 * 解析 ${...} 占位符，解析失败，而且没有默认值则抛出异常
	 */
	String resolveRequiredPlaceholders(String text) throws IllegalArgumentException;

}

```

## 1.2 BeanFactory相关

* ListableBeanFactory
* 可以枚举Bean的Bean工厂
* 通过名称、类型、注解等获取Bean以及Bean名称

```
public interface ListableBeanFactory extends BeanFactory {

	/**
	 * 是否包含Bean的定义
	 */
	boolean containsBeanDefinition(String beanName);

	/**
	 * 返回Bean定义的个数
	 */
	int getBeanDefinitionCount();

	/**
	 * 返回所有定义的Bean的名称
	 */
	String[] getBeanDefinitionNames();

	/**
	 * 返回当前Bean工厂中所有符合类型解析的Bean
	 */
	String[] getBeanNamesForType(ResolvableType type);

	/**
	 * 返回当前Bean工厂中所有该类型的Bean名称
	 */
	String[] getBeanNamesForType(Class<?> type);

	/**
	 * 返回当前Bean工厂中所有该类型的Bean名称
	 * 是否包含非单例的Bean，是否允许提前初始化懒加载的Bean以及FactoryBean创建的Bean
	 */
	String[] getBeanNamesForType(Class<?> type, boolean includeNonSingletons, boolean allowEagerInit);

	/**
	 * 返回当前Bean工厂中所有该类型的Bean名称与对应的Bean类型
	 */
	<T> Map<String, T> getBeansOfType(Class<T> type) throws BeansException;

	/**
	 * 类似
	 */
	<T> Map<String, T> getBeansOfType(Class<T> type, boolean includeNonSingletons, boolean allowEagerInit)
			throws BeansException;

	/**
	 * 返回所有具有该注解的Bean名称
	 */
	String[] getBeanNamesForAnnotation(Class<? extends Annotation> annotationType);

	/**
	 * 返回所有具有该注解的Bean名称以及对应的实例
	 */
	Map<String, Object> getBeansWithAnnotation(Class<? extends Annotation> annotationType) throws BeansException;

	/**
	 * 返回Bean上对应的注解
	 */
	<A extends Annotation> A findAnnotationOnBean(String beanName, Class<A> annotationType)
			throws NoSuchBeanDefinitionException;

}
```

* HierarchicalBeanFactory
* 包含层级关系的Bean工厂

```
public interface HierarchicalBeanFactory extends BeanFactory {

	/**
	 * 返回父Bean工厂
	 */
	BeanFactory getParentBeanFactory();

	/**
	 * 该Bean工厂中是否包含Bean
	 */
	boolean containsLocalBean(String name);

}
```

## 1.2.1 BeanFactory

* 最上层的Bean容器抽象
* 获取Bean
* 判断Bean 单例/原型
* 对比Bean的类型
* 获取Bean的别名

```
public interface BeanFactory {

	/**
	 * myJndiObject代表工厂Bean
	 * &myJndiObject代表该Factory
	 */
	String FACTORY_BEAN_PREFIX = "&";


	/**
	 * 通过名称获取Bean
	 */
	Object getBean(String name) throws BeansException;

	/**
	 * 获取该类型的Bean
	 */
	<T> T getBean(String name, Class<T> requiredType) throws BeansException;

	/**
	 * 获取Bean，同时带有初始化参数
	 */
	Object getBean(String name, Object... args) throws BeansException;

	/**
	 * 获取该类型的Bean
	 */
	<T> T getBean(Class<T> requiredType) throws BeansException;

	/**
	 * 通过名称获取Bean，同时带有初始化参数
	 */
	<T> T getBean(Class<T> requiredType, Object... args) throws BeansException;


	/**
	 * 是否包含Bean
	 */
	boolean containsBean(String name);

	/**
	 * 该Bean是否单例
	 */
	boolean isSingleton(String name) throws NoSuchBeanDefinitionException;

	/**
	 * 该Bean是否原型
	 */
	boolean isPrototype(String name) throws NoSuchBeanDefinitionException;

	/**
	 * Bean是否匹配类型
	 */
	boolean isTypeMatch(String name, ResolvableType typeToMatch) throws NoSuchBeanDefinitionException;

	/**
	 * Bean是否匹配类型
	 */
	boolean isTypeMatch(String name, Class<?> typeToMatch) throws NoSuchBeanDefinitionException;

	/**
	 * 获取Bean的类型
	 */
	Class<?> getType(String name) throws NoSuchBeanDefinitionException;

	/**
	 * 获取Bean的别名
	 */
	String[] getAliases(String name);

}
```

## 1.3 MessageSource

文本国际化解析

```
public interface MessageSource {

	/**
	 * 获取当地的文本，没有则返回默认
	 */
	String getMessage(String code, Object[] args, String defaultMessage, Locale locale);

	/**
	 * 获取当地的文本
	 */
	String getMessage(String code, Object[] args, Locale locale) throws NoSuchMessageException;

	/**
	 * 通过MessageSourceResolvable获取当地的文本
	 */
	String getMessage(MessageSourceResolvable resolvable, Locale locale) throws NoSuchMessageException;

}
```

### 1.3.1 MessageSourceResolvable

包含了解析文本所需要的信息

```
public interface MessageSourceResolvable {

	String[] getCodes();

	Object[] getArguments();

	String getDefaultMessage();
}
```

## 1.4 ApplicationEventPublisher

应用事件抽象

```
```

## 1.5 ResourcePatternResolver

统一资源解析

```
```