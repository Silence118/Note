# Spring

## 1.IOC的加载流程

* 初始化环境
* 刷新和创建BeanFactory
* 初始化BeanFactory
* 后置处理BeanFactory
* 调用BeanFactoryPostProcessor的后置处理
* 注册BeanPostProcessor
* 初始化消息源(国际化)
* 初始化事件多播器
* OnRefresh(): 初始化子容器的特殊的bean
* 注册监听器
* 创建单例Bean
* 发布相应的事件

## 2.Bean的生命周期

* InstantiationAwareBeanPostProcessor前置处理(判断是否需要提前Aop)
* 实例化
* InstantiationBeanPostProcessor后置处理
* 属性填充
* Aware回调（设置Aware的属性）
* BeanPostProcessor前置处理
* 初始化
* BeanPostProcessor后置处理(在这里完成Aop)
* 使用
* 销毁

## 3. 循环依赖

1. 通过三级缓存解决循环依赖
   * 一级缓存：singletonObjects 成品
   * 二级缓存：earlySingleObjects 半成品
   * 三级缓存：singletonFactories 存放lambda表达式

2. A依赖B，B依赖A
   * 实例化A并放入三级缓存
   * A属性填充
   * 实例化B并放入三级缓存
   * B属性填充
   * 从三级缓存中获取A实例，并放入二级缓存
   * 初始化B并放入一级缓存
   * A从一级缓存中获取B属性填充完成
   * 初始化A并放入一级缓存