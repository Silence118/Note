### 01-maven概述(掌握)

* 为什么?
  * 之前项目中会有大量的jar包，面临难以维护：①找到这些jar包比较麻烦；②还会存在版本冲突问题
* 组成
  * ①项目对象模型：pom , project object model
    * 根据maven的核心配置文件pom.xml生成
  * ②依赖管理：dependency
    * 对jar包进行统一管理
  * ③构建生命周期 : build project lifecycle
    * 使用插件对项目的生命周期管理
* 作用
  * ①对依赖进行统一管理
  * ②使用maven快速构建项目
  * ③统一项目目录结构







### 02-maven环境搭建(掌握)

* 下载
  * [Index of /dist/maven/maven-3 (apache.org)](https://archive.apache.org/dist/maven/maven-3/)
* 推荐
  * idea2019 + maven3.5.4
  * idea2020及以上 + maven3.6.3
* 安装
  * 直接解压
* 配置环境变量
  * MAVEN_HOME
  * ![image-20211218090448210](https://typora2020.oss-cn-beijing.aliyuncs.com/image-20211218090448210.png)





### 03-maven目录结构(了解)

* 目录结构
  * bin
    * 存储maven的可执行文件
  * boot
    * 存储maven运行所需的类加载器
  * conf
    * 存储maven的配置文件，比如：settings.xml
  * lib
    * 存储maven运行所需的jar包





### 04-maven仓库(掌握)

* 分类
  * ![image-20211218092130040](https://typora2020.oss-cn-beijing.aliyuncs.com/image-20211218092130040.png)

* 本地仓库
  * 开发者自己电脑上存储资源的仓库，也可从远程仓库获取资源
* 私服
  * 远程仓库的一种
  * 中央仓库受带宽限制，且服务器架设在国外，下载速度非常慢，私服可以提升jar包下载速 度；
  * 保存具有版权的资源，包含购买或自主研发的jar；
  * 一定范围内共享资源，能做到仅对内不对外开放。
* 中央仓库
  * 远程仓库的一种
  * maven团队自身维护的仓库，属于开源的





### 05-maven坐标(掌握)

* 概述
  * 用于确定jar包在仓库中的位置
* 组成
  * ①groupid : 公司名称
  * ②artifactid : 项目名称
  * ③version：版本名称





### 06-maven仓库设置(掌握)

* 概述

  * 开发人员，需要设置maven本地仓库、maven中央仓库的镜像。

* maven本地仓库

  * 默认路径是在"${user.home}/.m2/repository"，系统盘符的存储空间比较珍贵，所以需要重新设置本地仓库的路径

  ```xml
  <settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">
  	<localRepository>E:\mavenrepository\repository</localRepository>
  </settings>
  ```

* maven中央仓库

  * 中央仓库是由maven官方团队维护，服务器架设在国外，下载速度非常慢。

  ```xml
  <mirrors>
      <mirror>
          <id>nexus-aliyun</id>
          <mirrorOf>central</mirrorOf>
          <name>Nexus aliyun</name>
          <url>http://maven.aliyun.com/nexus/content/groups/public</url>
      </mirror>
  
  </mirrors>
  ```

  



### 07-maven项目结构(掌握)

* javase项目结构
  * src
    * main
      * java
      * resources
    * test
      * java
      * resources
  * pom.xml
* javaweb项目结构
  * src
    * main
      * java
      * resources
      * webapps : 页面资源
    * test
      * java
      * resources
  * pom.xml





### 08-插件创建maven项目(了解)

* 插件创建javase项目
  * ![image-20211218094322919](https://typora2020.oss-cn-beijing.aliyuncs.com/image-20211218094322919.png)
* 插件创建javaweb项目
  * ![image-20211218094607404](https://typora2020.oss-cn-beijing.aliyuncs.com/image-20211218094607404.png)





### 09-idea配置maven(掌握)

* ①File -> settings for new projects
* ②build,execution,deployement -> build tools -> maven
* ③配置maven插件
  * ![image-20211218101552326](https://typora2020.oss-cn-beijing.aliyuncs.com/image-20211218101552326.png)



### 10-idea创建maven的javase项目(掌握)

* ![image-20211218101751193](https://typora2020.oss-cn-beijing.aliyuncs.com/image-20211218101751193.png)

* ![image-20211218101834326](https://typora2020.oss-cn-beijing.aliyuncs.com/image-20211218101834326.png)

* ![image-20211218101913326](https://typora2020.oss-cn-beijing.aliyuncs.com/image-20211218101913326.png)

* **![image-20211218102346189](https://typora2020.oss-cn-beijing.aliyuncs.com/image-20211218102346189.png)**







### 11-idea创建maven的javaweb项目(掌握)

* ![image-20211218102458539](https://typora2020.oss-cn-beijing.aliyuncs.com/image-20211218102458539.png)

* ![image-20211218102533016](https://typora2020.oss-cn-beijing.aliyuncs.com/image-20211218102533016.png)

* ![image-20211218102544919](C:\Users\qiuzhiwei\AppData\Roaming\Typora\typora-user-images\image-20211218102544919.png)

* ![image-20211218102732978](https://typora2020.oss-cn-beijing.aliyuncs.com/image-20211218102732978.png)





### 12-maven运行tomcat(掌握)

* ①本地tomcat插件

* ②内置tomcat插件

  * 默认使用的tomcat6.0版本，会有版本兼容问题，要求设置为tomcat7.0

  ```xml
  <build>
      <plugins>
          <!--maven的tomcat插件-->
          <plugin>
              <groupId>org.apache.tomcat.maven</groupId>
              <artifactId>tomcat7-maven-plugin</artifactId>
              <version>2.2</version>
              <configuration>
                  <!--项目访问路径-->
                  <path>/mavenweb</path>
                  <!--项目访问端口-->
                  <port>8080</port>
              </configuration>
          </plugin>
      </plugins>
  </build>
  ```

* ③其他细节

  ```xml
  <dependencies>
  
      <!--服务器编译环境-->
      <dependency>
          <groupId>javax.servlet</groupId>
          <artifactId>javax.servlet-api</artifactId>
          <version>4.0.1</version>
          <scope>provided</scope>
      </dependency>
      <dependency>
          <groupId>javax.servlet.jsp</groupId>
          <artifactId>javax.servlet.jsp-api</artifactId>
          <version>2.3.3</version>
          <scope>provided</scope>
      </dependency>
  
  </dependencies>
  ```

  ```xml
  <!--jdk的编译版本-->
  <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-compiler-plugin</artifactId>
      <version>3.8.1</version>
      <configuration>
          <source>1.8</source>
          <target>1.8</target>
          <encoding>UTF-8</encoding>
      </configuration>
  </plugin>
  ```

  





### 13-maven生命周期与插件(掌握)

* 生命周期

  * ![image-20211218111051890](https://typora2020.oss-cn-beijing.aliyuncs.com/image-20211218111051890.png)

  * mvn clean
    * 删除target目录
  * mvn compile
    * 编译maven项目，将生成的资源部署到target目录
  * mvn test
    * compile -> test , 执行所有的单元测试
  * mvn package
    * compile -> test -> package , 将当前项目打包(jar/war)
  * mvn install
    * compile -> test -> package -> install , 将jar/war包安装到本地仓库





### 14-maven依赖管理(了解)





### 15-依赖范围scope(掌握)

* 概述

  * 规定依赖/构件的作用范围

* 依赖范围

  | 依赖范围 | 主代码 | 测试代码 | 包   | 例子                 |
  | -------- | ------ | -------- | ---- | -------------------- |
  | compile  | ok     | ok       | ok   | 默认值，lombok       |
  | test     |        | ok       |      | junit                |
  | provided | ok     | ok       |      | servlet-api、jsp-api |
  | runtime  |        | ok       | ok   | jdbc                 |

  



### 16-依赖传递(了解)

* 概述

  * 构件A依赖构件B，构件B依赖构件C，那么构件A传递依赖构件C

* 依赖传递

  ![image-20211218142156608](https://typora2020.oss-cn-beijing.aliyuncs.com/image-20211218142156608.png)





### 17-依赖排除(掌握)

* 概述

  * 构件依赖另外一个构件时，会有一些传递依赖，如果对其中的一些传递依赖不需要，就可以做依赖排除。

* 代码实现

  ```xml
  <dependency>
      <groupId>com.atguigu</groupId>
      <artifactId>test2</artifactId>
      <version>1.0.0-SNAPSHOT</version>
      <scope>compile</scope>
      <!--排除test3-->
      <!--
      <exclusions>
          <exclusion>
              <groupId>com.atguigu</groupId>
              <artifactId>test3</artifactId>
          </exclusion>
      </exclusions>
      -->
  </dependency>
  ```

  ```xml
  <dependency>
      <groupId>com.atguigu</groupId>
      <artifactId>test3</artifactId>
      <version>1.0.0-SNAPSHOT</version>
      <scope>compile</scope>
      <!--隐藏依赖-->
      <optional>true</optional>
  </dependency>
  ```





### 18-依赖传递冲突问题(掌握)

* 概述
  * 依赖传递中,如果依赖了不同版本的相同构件,那么就会造成版本冲突
* ①路径优先
  * 当依赖中出现相同的资源时，层级越深，优先级越低，层级越浅，优先级越高
* ②声明优先
  * 当资源在相同层级被依赖时，配置顺序靠前的覆盖配置顺序靠后的

* ③版本锁定

  ```
  <dependencyManagement>
      <dependencies>
          <dependency>
              <groupId>org.projectlombok</groupId>
              <artifactId>lombok</artifactId>
              <version>1.18.22</version>
          </dependency>
      </dependencies>
  </dependencyManagement>
  ```





### 19-属性properties(掌握)

* 概述

  * 在maven中定义变量

* 代码实现

  ```xml
  <properties>
      <lombok.version>1.18.22</lombok.version>
      <servlet.version>4.0.1</servlet.version>
      <jsp.version>2.3.3</jsp.version>
      <junit.version>4.13.2</junit.version>
      <dbutils.version>1.7</dbutils.version>
      <druid.version>1.2.6</druid.version>
      <mysql.version>5.1.48</mysql.version>
  </properties>
  
  <dependencies>
  
      <dependency>
          <groupId>org.projectlombok</groupId>
          <artifactId>lombok</artifactId>
          <version>${lombok.version}</version>
      </dependency>
  
      <!--服务器编译环境-->
      <dependency>
          <groupId>javax.servlet</groupId>
          <artifactId>javax.servlet-api</artifactId>
          <version>${servlet.version}</version>
          <scope>provided</scope>
      </dependency>
      <dependency>
          <groupId>javax.servlet.jsp</groupId>
          <artifactId>javax.servlet.jsp-api</artifactId>
          <version>${jsp.version}</version>
          <scope>provided</scope>
      </dependency>
  
      <!--junit-->
      <dependency>
          <groupId>junit</groupId>
          <artifactId>junit</artifactId>
          <version>${junit.version}</version>
          <scope>test</scope>
      </dependency>
  
  
      <dependency>
          <groupId>commons-dbutils</groupId>
          <artifactId>commons-dbutils</artifactId>
          <version>${dbutils.version}</version>
      </dependency>
  
      <dependency>
          <groupId>com.alibaba</groupId>
          <artifactId>druid</artifactId>
          <version>${druid.version}</version>
      </dependency>
  
      <dependency>
          <groupId>mysql</groupId>
          <artifactId>mysql-connector-java</artifactId>
          <version>${mysql.version}</version>
      </dependency>
  
  
  </dependencies>
  ```

* 推荐
  * maven-search





### 20-版本管理version(掌握)

* 概述

  ```
  主版本.次版本.增量版本-里程杯版本
  
  主版本: 项目的结构发生重大改变
  次版本: 有新的重大的功能发布
  增量版本: 修复BUG
  里程杯版本: SNAPSHOT/RELEASE,开发人员看的内部版本
  ```

  





### 21-构件下载失败处理(掌握)

* 概述

  * 构件在下载过程中，会产生一个xxx.jar.lastUpdated临时文件，下载完成后，会将该临时文件修改为xxx.jar；
  * 如果下载过程中，网络发生了问题，xxx.jar.lastUpdated临时文件就不会修改为xxx.jar，导致下载失败。
  * 需要开发人员去清除这些临时文件。

* 解决方案

  * ①将clearLastUpdated.bat文件存放到maven本地仓库的根目录

  * ②打开clearLastUpdated.bat文件，编辑设置

    ```
    SET CLEAR_PATH=本地仓库所在盘符名
    SET CLEAR_DIR=本地仓库所在路径
    ```

  * ③运行clearLastUpdated.bat文件

    * ![image-20211218153428336](https://typora2020.oss-cn-beijing.aliyuncs.com/image-20211218153428336.png)







### 22-分模块构建工程(掌握)

* 分模块工程
  * ![image-20211218154802309](https://typora2020.oss-cn-beijing.aliyuncs.com/image-20211218154802309.png)





### 23-聚合工程之mvn-parent(掌握)

* 开发步骤

  * ①创建maven项目
  * ②编写pom.xml
    * 版本锁定
    * 打包方式=pom

* ②编写pom.xml

  ```xml
  <groupId>com.atguigu</groupId>
  <artifactId>mvn-parent</artifactId>
  <version>1.0.0-SNAPSHOT</version>
  <packaging>pom</packaging>
  
  
  
  <properties>
      <lombok.version>1.18.22</lombok.version>
      <servlet.version>4.0.1</servlet.version>
      <jsp.version>2.3.3</jsp.version>
      <junit.version>4.13.2</junit.version>
      <dbutils.version>1.7</dbutils.version>
      <druid.version>1.2.6</druid.version>
      <mysql.version>5.1.48</mysql.version>
      <thymeleaf.version>3.0.12.RELEASE</thymeleaf.version>
  </properties>
  
  
  <dependencyManagement>
      <dependencies>
  
          <dependency>
              <groupId>org.thymeleaf</groupId>
              <artifactId>thymeleaf</artifactId>
              <version>${thymeleaf.version}</version>
          </dependency>
          <dependency>
              <groupId>org.projectlombok</groupId>
              <artifactId>lombok</artifactId>
              <version>${lombok.version}</version>
          </dependency>
  
          <!--服务器编译环境-->
          <dependency>
              <groupId>javax.servlet</groupId>
              <artifactId>javax.servlet-api</artifactId>
              <version>${servlet.version}</version>
              <scope>provided</scope>
          </dependency>
          <dependency>
              <groupId>javax.servlet.jsp</groupId>
              <artifactId>javax.servlet.jsp-api</artifactId>
              <version>${jsp.version}</version>
              <scope>provided</scope>
          </dependency>
  
          <!--junit-->
          <dependency>
              <groupId>junit</groupId>
              <artifactId>junit</artifactId>
              <version>${junit.version}</version>
              <scope>test</scope>
          </dependency>
  
  
          <dependency>
              <groupId>commons-dbutils</groupId>
              <artifactId>commons-dbutils</artifactId>
              <version>${dbutils.version}</version>
          </dependency>
  
          <dependency>
              <groupId>com.alibaba</groupId>
              <artifactId>druid</artifactId>
              <version>${druid.version}</version>
          </dependency>
  
          <dependency>
              <groupId>mysql</groupId>
              <artifactId>mysql-connector-java</artifactId>
              <version>${mysql.version}</version>
          </dependency>
  
  
      </dependencies>
  
  </dependencyManagement>
  ```





### 24-聚合工程之mvn-dao(掌握)

* 开发步骤
  * ①创建子模块mvn-dao
    * 以mvn-parent为父工程
    * 打包方式=jar
  * ②定义dao接口及其实现子类

* ①创建子模块mvn-dao

  ```xml
  <!--继承-->
  <parent>
      <artifactId>mvn-parent</artifactId>
      <groupId>com.atguigu</groupId>
      <version>1.0.0-SNAPSHOT</version>
  </parent>
  <modelVersion>4.0.0</modelVersion>
  
  <artifactId>mvn-dao</artifactId>
  <packaging>jar</packaging>
  
  <dependencies>
  
  
      <dependency>
          <groupId>org.projectlombok</groupId>
          <artifactId>lombok</artifactId>
      </dependency>
  
      <!--junit-->
      <dependency>
          <groupId>junit</groupId>
          <artifactId>junit</artifactId>
          <scope>test</scope>
      </dependency>
  
  
      <dependency>
          <groupId>commons-dbutils</groupId>
          <artifactId>commons-dbutils</artifactId>
      </dependency>
  
      <dependency>
          <groupId>com.alibaba</groupId>
          <artifactId>druid</artifactId>
      </dependency>
  
      <dependency>
          <groupId>mysql</groupId>
          <artifactId>mysql-connector-java</artifactId>
      </dependency>
  
  
  </dependencies>
  ```

* ②定义dao接口及其实现子类





### 25-聚合工程之mvn-service(掌握)

* 开发步骤

  * ①创建子模块mvn-service
    * 以mvn-parent为父工程
    * 打包方式=jar
  * ②定义service接口及其实现子类

* ①创建子模块mvn-service

  ```xml
  <parent>
      <artifactId>mvn-parent</artifactId>
      <groupId>com.atguigu</groupId>
      <version>1.0.0-SNAPSHOT</version>
  </parent>
  <modelVersion>4.0.0</modelVersion>
  
  <artifactId>mvn-service</artifactId>
  <packaging>jar</packaging>
  
  <dependencies>
  
  
      <!--junit-->
      <dependency>
          <groupId>junit</groupId>
          <artifactId>junit</artifactId>
          <scope>test</scope>
      </dependency>
  
      <dependency>
          <groupId>com.atguigu</groupId>
          <artifactId>mvn-dao</artifactId>
          <version>1.0.0-SNAPSHOT</version>
      </dependency>
  
  </dependencies>
  ```

* ②定义service接口及其实现子类

  



### 26-聚合工程mvn-controller模块(掌握)

* 开发步骤

  * ①创建子模块mvn-controller
    * archetype=maven-archetype-webapp
    * 以mvn-parent为父工程
    * 打包方式=war
  * ②定义controller

* ①创建子模块mvn-controller

  ```xml
  <parent>
      <artifactId>mvn-parent</artifactId>
      <groupId>com.atguigu</groupId>
      <version>1.0.0-SNAPSHOT</version>
  </parent>
  <modelVersion>4.0.0</modelVersion>
  
  <artifactId>mvn-controller</artifactId>
  <packaging>war</packaging>
  
  <dependencies>
  
      <dependency>
          <groupId>org.thymeleaf</groupId>
          <artifactId>thymeleaf</artifactId>
      </dependency>
  
      <!--服务器编译环境-->
      <dependency>
          <groupId>javax.servlet</groupId>
          <artifactId>javax.servlet-api</artifactId>
          <scope>provided</scope>
      </dependency>
      <dependency>
          <groupId>javax.servlet.jsp</groupId>
          <artifactId>javax.servlet.jsp-api</artifactId>
          <scope>provided</scope>
      </dependency>
  
      <!--junit-->
      <dependency>
          <groupId>junit</groupId>
          <artifactId>junit</artifactId>
          <scope>test</scope>
      </dependency>
  
  
      <dependency>
          <groupId>com.atguigu</groupId>
          <artifactId>mvn-service</artifactId>
          <version>1.0.0-SNAPSHOT</version>
      </dependency>
  
  </dependencies>
  ```

* ②定义controller

  