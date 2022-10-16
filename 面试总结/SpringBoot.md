# SpringBoot

## 1.生成一个SpringApplication对象

* 推测web应用类型(NONE、REACTIVE、SERVLET)

* 从Spring.factories中获取ApplicationContextInitalizer
* 从Spring.factoreis中获取ApplicationListener

## 2.SpringApplication的run方法

* 从Spring.factories中获取SpringApplicatoinRunListener并注册监听器
* 发布ApplicationStartingEvent
* 创建并配置环境
* 发布ApplicationEnvirionmentPreapredEvnet
* 创建Spring容器
* 初始化Spring容器(设置环境，后置处理Spring容器)
* 发布ApplicationContextInitializedEvent
* 发布ApplicationPreparedEvent
* 刷新Spring容器
* 发布SpringApplicationStartedEvent
* 回调ApplicationRunner和ComandLineRunner

## 3. SpringBoot解析配置类顺序

* 自定义配置类， Compoent类
* AutoConfigurationImportSelector导入的自读配置类

## 4. SpringMVC异常处理

* 使用@ControllerAdvice + @ExceptionHandler标注处理异常的方法以及返回的视图
* 使用@ResponseStatus + 自定义异常类，ResponseStatusExceptionResolver将解析异常类
* 自定义HandlerExceptionResolver

## 5. 注入原生的ServletAPI

* 使用@ServletComponentScan + 自定义原生API扫描
* 使用配置类 + @Bean自定义 ServletAPIRegistrationBean

## 6.自动配置原理

* 在ConfgiurrationClassPostProcessor中扫描配置类中的Import注解，加载AutoConfigurationImportSelector这个类，这个类实现了DeferdImportSelector接口，执行其中的process方法，会去加载MET-INF/spring.factories中的以EnableAutoConfiguration为key的类从而实现自动配置
* @EnableAutoConfiguration注解