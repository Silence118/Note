## MyBatis

### 1.原始JDBC存在问题

- 存在问题
    - ①频繁地创建和销毁Connection对象
    - ②driverClass、url、username、password、sql语句都存在字符串硬编码
    - ③手动设置参数
        - 输入映射：将输入的值映射到占位符
    - ④手动处理结果集
        - 输出映射：将结果集映射到JavaBean
    - ⑤手动释放资源
- 解决方案(使用mybatis)
    - ①引入连接池
    - ②使用配置文件
    - ③使用反射+缺省操作
    - ④使用反射+缺省操作
    - ⑤缺省操作

### 2.mybatis概述

- 概述

    - mybatis是一个优秀的基于java的持久层框架，它内部封装了jdbc,使开发者只需要关注sql语句本身，而不需要花费精力去处理加载驱动、创建连接等繁杂的过程

- 结构

    ![mybatis结构](F:\Note\framework\Mybatis.assets\mybatis结构.jpg)



### 3.mybatis入门程序

- 官网

    [mybatis - MyBatis 3 | 入门](https://mybatis.org/mybatis-3/zh/getting-started.html)

- 开发步骤
    - ①引入mybatis相关依赖
    - ②创建mybatis的核心配置文件
        - 配置数据库环境(事务管理器、数据源)
        - 加载mapper映射文件
    - ③创建mapper映射配置文件
        - 配置MappedStatement
    - ④定义dao接口及其实现子类
        - 1,读取mybatis的核心配置文件
        - 2,创建SqlSessionFactory对象
        - 3,获取SqlSession对象
        - 4,操作对应的MappedStatement对象
        - 5,释放资源
    - ⑤代码测试

- ①引入mybatis相关依赖

    ```xml
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.5.7</version>
    </dependency>
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>5.1.48</version>
    </dependency>
    ```

- ②创建mybatis的核心配置文件

    ```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE configuration
            PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
            "http://mybatis.org/dtd/mybatis-3-config.dtd">
    <configuration>
        <environments default="development">
            <environment id="development">
                <!--事务管理器-->
                <transactionManager type="JDBC"/>
                <!--数据源-->
                <dataSource type="POOLED">
                    <property name="driver" value="com.mysql.jdbc.Driver"/>
                    <property name="url" value="jdbc:mysql://localhost:3306/mydb1"/>
                    <property name="username" value="root"/>
                    <property name="password" value="root"/>
                </dataSource>
            </environment>
        </environments>
    
        <!--加载映射配置文件-->
        <mappers>
            <mapper resource="mapper01.xml"/>
        </mappers>
    </configuration>
    ```

- ③创建mapper映射配置文件

    ```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE mapper
            PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
            "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    <mapper namespace="test">
        <!--
            配置MappedStatement
            id:statement唯一标识
            parameterType: 输入映射的类型
            resultType: 输出映射的类型
        -->
        <select id="selectExamById" parameterType="int" resultType="com.atguigu.pojo.Exam">
            select * from tb_exam where id = #{id}
        </select>
    </mapper>
    ```

    ④定义dao接口及其实现子类

    ```java
    public class ExamDaoImpl implements ExamDao {
        @Override
        public Exam selectExamById(Integer id) throws Exception {
            //1，读取mybatis核心配置文件
            InputStream inputStream = Resources.getResourceAsStream("SqlMapConfig.xml");
            //2，获取SqlSessionFactory对象
            SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
            //3，打开会话
            SqlSession sqlSession = sqlSessionFactory.openSession();
            //4，执行MappedStatement
            Exam exam = sqlSession.selectOne("selectExamById", id);
            //5，关闭会话
            sqlSession .close();
            return exam;
        }
    }
    ```

    ⑤代码测试

    ```java
    public class ExamDaoTest {
    
        @Test
        public void selectExamById() throws Exception {
    
            ExamDao examDao = new ExamDaoImpl();
            Exam exam = examDao.selectExamById(1);
            System.out.println("exam = " + exam);
    
        }
    }
    ```


### 4.mapper映射文件说明

- 映射文件
  
    ![image-20211220101926370](F:\Note\framework\Mybatis.assets\image-20211220101926370.png)
    

### 5.添加用户

- 代码实现
  
    ```xml
    <!--添加用户-->
    <insert id="addExam" parameterType="com.atguigu.pojo.Exam">
        insert into tb_exam
        values (null, #{name}, #{math}, #{english}, #{chinese})
    </insert>
    ```
    
    ```java
    @Override
    public int addExam(Exam inputExam) throws Exception {
        //1，读取mybatis核心配置文件
        InputStream inputStream = Resources.getResourceAsStream("SqlMapConfig.xml");
        //2，获取SqlSessionFactory对象
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        //3，打开会话
        SqlSession sqlSession = sqlSessionFactory.openSession();
        //4，执行MappedStatement
        int addExam = sqlSession.insert("addExam", inputExam);
        //5，提交事务
        sqlSession.commit();
        //6，关闭会话
        sqlSession.close();
        return addExam;
    }
    ```
    
- 注意事项

    - mybatis的事务是手动提交

### 6.删除用户

- 代码实现
  
    ```xml
    <delete id="deleteExamById" parameterType="int">
        delete
        from tb_exam
        where id = #{id}
    </delete>
    ```
    
    ```java
    @Override
    public int deleteExamById(Integer id) throws Exception {
        //1，读取mybatis核心配置文件
        InputStream inputStream = Resources.getResourceAsStream("SqlMapConfig.xml");
        //2，获取SqlSessionFactory对象
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        //3，打开会话
        SqlSession sqlSession = sqlSessionFactory.openSession();
        //4，执行MappedStatement
        int deleteExamById = sqlSession.insert("deleteExamById", id);
        //5，提交事务
        sqlSession.commit();
        //6，关闭会话
        sqlSession.close();
        return deleteExamById;
    }
    ```
    

### 7.修改用户

- 代码实现
  
    ```xml
    <update id="updateExamById" parameterType="com.atguigu.pojo.Exam">
        update tb_exam
        set name    = #{name},
            math    = #{math},
            english = #{english},
            chinese = #{chinese}
        where id = #{id}
    </update>
    ```
    
    ```java
    @Override
    public int updateExamById(Exam inputExam) throws Exception {
        //1，读取mybatis核心配置文件
        InputStream inputStream = Resources.getResourceAsStream("SqlMapConfig.xml");
        //2，获取SqlSessionFactory对象
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        //3，打开会话
        SqlSession sqlSession = sqlSessionFactory.openSession();
        //4，执行MappedStatement
        int updateExamById = sqlSession.insert("updateExamById", inputExam);
        //5，提交事务
        sqlSession.commit();
        //6，关闭会话
        sqlSession.close();
        return updateExamById;
    }
    ```
    

### 8.Resources类概述

- 概述
    - 用来读取资源的工具类

### 9.SqlSessionFactoryBuilder类概述

- 概述
    - 这个类可以被实例化，使用和丢弃，一旦创建了SqlSessionFactory，就不再需要它了
    - 但最好还是不要一直保留它
    - 最好是一个方法内的匿名对象

### 10.SqlSessionFactory接口概述

- 概述
    - SqlSessionFactory一旦被创建就应该在应用的运行期间一直存在，没有任何理由丢弃它或重新创建另一个实例
    - 使用SqlSessionFactory的最佳时间是在应用运行期间，不要重复创建多次，多次创建SqlSessionFactory被视为一种代码“坏习惯”。因此SqlSessionFactory的最佳作用域是应用作用域

### 11.SqlSession接口概述

- 概述
    - 每个线程都应该有它自己的SqlSession实例。SqlSession的实例不是线程安全的，因此是不能被共享的，所以它的最佳作用域是请求或方法作用域
    - 一次数据库操作就应该是一个新的SqlSession对象

### 12.mybatis传统dao开发

- 概述
    - mybatis开发有两种方式：1.传统dao开发; 2.mapper接口代理开发
    - 传统dao开发：dao接口 + dao接口实现子类 + mapper映射文件
- 代码实现
  
    ```java
    public class ExamDaoImpl implements ExamDao {
       
    	  private SqlSessionFactory sqlSessionFactory ;
    
        //后续和spring框架整合之后，就可以保证sqlSessionFactory对象是一个单例对象
        public ExamDaoImpl(SqlSessionFactory sqlSessionFactory) {
            this.sqlSessionFactory = sqlSessionFactory;
        }
    
        @Override
        public Exam selectExamById(Integer id) throws Exception {
    
            //3，打开会话
            SqlSession sqlSession = sqlSessionFactory.openSession();
            //4，执行MappedStatement
            Exam exam = sqlSession.selectOne("selectExamById", id);
            //5，关闭会话
            sqlSession .close();
            return exam;
        }
    
        @Override
        public int addExam(Exam inputExam) throws Exception {
    
            //3，打开会话
            SqlSession sqlSession = sqlSessionFactory.openSession();
            //4，执行MappedStatement
            int addExam = sqlSession.insert("addExam", inputExam);
            //5，提交事务
            sqlSession.commit();
            //6，关闭会话
            sqlSession.close();
            return addExam;
        }
    
        @Override
        public int deleteExamById(Integer id) throws Exception {
    
            //3，打开会话
            SqlSession sqlSession = sqlSessionFactory.openSession();
            //4，执行MappedStatement
            int deleteExamById = sqlSession.delete("deleteExamById", id);
            //5，提交事务
            sqlSession.commit();
            //6，关闭会话
            sqlSession.close();
            return deleteExamById;
        }
    
        @Override
        public int updateExamById(Exam inputExam) throws Exception {
    
            //3，打开会话
            SqlSession sqlSession = sqlSessionFactory.openSession();
            //4，执行MappedStatement
            int updateExamById = sqlSession.update("updateExamById", inputExam);
            //5，提交事务
            sqlSession.commit();
            //6，关闭会话
            sqlSession.close();
            return updateExamById;
        }
    }
    ```
    
- 存在问题
    - ①mapper映射文件和dao实现子类的耦合度过高
    - ②开发中会有大量的dao实现子类，难以维护

### 13.mybatis接口代理开发

- 概述

    - dao接口 + mapper映射文件
- 开发步骤
    - ①mapper映射文件要和接口放在同一个目录中
    - ②mapper映射文件要和接口名称一致
    - ③mapper映射文件的namespace和接口的全类名一致
    - ④MappedStatement的id和接口的方法名称一致
    - ⑤MappedStatement的paramType和接口方法的形参类型一致
    - ⑥MappedStatement的returnType和接口方法的返回值类型一致
    - ⑦代码测试
- 代码实现
  
    ```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE mapper
            PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
            "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    <mapper namespace="com.atguigu.dao.ExamDao">
        <select id="selectExamById" parameterType="int" resultType="com.atguigu.pojo.Exam">
            select *
            from tb_exam
            where id = #{id}
        </select>
    
        <!--添加exam-->
        <insert id="addExam" parameterType="com.atguigu.pojo.Exam">
            insert into tb_exam
            values (null, #{name}, #{math}, #{english}, #{chinese})
        </insert>
    
        <!--删除exam-->
        <delete id="deleteExamById" parameterType="int">
            delete
            from tb_exam
            where id = #{id}
        </delete>
    
        <!--修改exam-->
        <update id="updateExamById" parameterType="com.atguigu.pojo.Exam">
            update tb_exam
            set name   = #{name},
                math    = #{math},
                english = #{english},
                chinese = #{chinese}
            where id = #{id}
        </update>
    </mapper>
    ```
    
    ```java
    public class ExamDaoTest {
    
        private SqlSessionFactory sqlSessionFactory;
    
        @Before
        public void init() throws IOException {
            sqlSessionFactory = new SqlSessionFactoryBuilder()
                    .build(Resources.getResourceAsStream("SqlMapConfig.xml"));
        }
    
        @Test
        public void selectExamById() throws Exception {
            SqlSession sqlSession = sqlSessionFactory.openSession();
            ExamDao examDao = sqlSession.getMapper(ExamDao.class);
            Exam exam = examDao.selectExamById(2);
            System.out.println("exam = " + exam);
            sqlSession.close();
        }
    }
    ```
    

### 14.mybatis-config配置文件解释

- 概述
    - properties
        - 用于在mybatis中声明变量
    - settings
        - 缓存、懒加载、日志实现等等
    - typeAlias
        - 设置别名
    - plugins
        - 设置插件
    - environments
        - 事务管理器、数据源
    - mappers
        - 加载mapper映射文件

### 15.mybatis内置连接池

- 概述
    - 在Mybatis中也有连接池技术，但是它采用的是自己的连接池技术。在mybatis的mybatis-config.xml配置文件中，通过配置<DataSource>标签实现连接池的配置
- 分类
    - JndiDataSourceFactory
    - PooledDataSource
    - UnpooledDataSource

### 16.environments标签

- 组成
    - transactionManager
        - JDBC：有事务管理
        - MANAGED：无事务管理
    - dataSource
        - POOLED
        - UNPOOLED
- 代码实现
  
    ```xml
    <environments default="development2">
    
        <environment id="development">
            <!--事务管理器-->
            <transactionManager type="JDBC"/>
    
            <!--数据源-->
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mydb1"/>
                <property name="username" value="root"/>
                <property name="password" value="root"/>
            </dataSource>
    
        </environment>
    
        <environment id="development2">
            <!--事务管理器-->
            <transactionManager type="JDBC"/>
    
            <!--数据源-->
            <dataSource type="UNPOOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mydb1"/>
                <property name="username" value="root"/>
                <property name="password" value="root"/>
            </dataSource>
    
        </environment>
    </environments>
    ```
    

### 17.properties标签

- 概述

    - 在mybatis容器中声明变量并复用
- 代码实现
  
    ```xml
    <properties>
        <property name="driverClass" value="com.mysql.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/mydb1"/>
        <property name="username" value="root"/>
        <property name="password" value="root"/>
    </properties>
    <environments default="development">
        <environment id="development">
            <!--事务管理器-->
            <transactionManager type="JDBC"/>
    
            <!--数据源-->
            <dataSource type="POOLED">
                <property name="driver" value="${driverClass}"/>
                <property name="url" value="${url}"/>
                <property name="username" value="${username}"/>
                <property name="password" value="${password}"/>
            </dataSource>
        </environment>
    </environments>
    ```
    
    ```xml
    <properties resource="jdbc.properties"></properties>
    <!--整合spring之后弃用-->
    <environments default="development">
        <environment id="development">
            <!--事务管理器-->
            <transactionManager type="JDBC"/>
    
            <!--数据源-->
            <dataSource type="POOLED">
                <property name="driver" value="${driverClass}"/>
                <property name="url" value="${url}"/>
                <property name="username" value="${username}"/>
                <property name="password" value="${password}"/>
            </dataSource>
        </environment>
    </environments>
    ```
    

### 18.settings标签之log4j

- 概述

    - 在日常开发过程中，排查问题时难免需要输出mybatis真正执行的SQL语句、参数、结果等信息，我们就可借助log4j的概念来实现执行信息的输出
- 开发步骤
    - ①引入log4j相关依赖
    - ②引入log4j.xml配置文件
    - ③配置mybatis的日志实现
- ①引入log4j相关依赖
  
    ```xml
    <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
        <version>1.2.17</version>
    </dependency>
    ```
    
- ②引入log4j.xml配置文件
  
    ```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE log4j:configuration SYSTEM "log4j.dtd">
    <log4j:configuration xmlns:log4j="http://jakarta.apache.org/log4j/">
    
        <appender name="STDOUT" class="org.apache.log4j.ConsoleAppender">
            <param name="Encoding" value="UTF-8"/>
            <layout class="org.apache.log4j.PatternLayout">
                <param name="ConversionPattern" value="%-5p %d{MM-dd HH:mm:ss,SSS} %m  (%F:%L) \n"/>
            </layout>
        </appender>
        <logger name="java.sql">
            <level value="debug"/>
        </logger>
        <logger name="org.apache.ibatis">
            <level value="info"/>
        </logger>
    
        <!--开启全局日志打印-->
        <root>
            <level value="debug"/>
            <appender-ref ref="STDOUT"/>
        </root>
    </log4j:configuration>
    ```
    
- ③配置mybatis的日志实现
  
    ```xml
    <properties resource="jdbc.properties"></properties>
    ```
    

### 19.settings标签之mapUnderscoreToCamelCase

- 概述
    - 数据库中，表的字段名称为：a_column; java中，类的名称为：aColumn
    - 如果设置mapUnderscoreToCamleCase=true，a_column字段就会自动映射到aColumn
- 代码实现
  
    ```xml
    <settings>
        <setting name="mapUnderscoreToCamelCase" value="true"/>
    </settings>
    ```
    

### 20.typeAliases标签

- 概述

    - 设置java类型的别名
- 代码实现
  
    ```xml
    <typeAliases>
        <typeAlias type="com.atguigu.pojo.User" alias="user" ></typeAlias>
        <typeAlias type="com.atguigu.pojo.Exam" alias="exam" ></typeAlias>
    </typeAliases>
    ```
    
    ```xml
    <typeAliases>
        <package name="com.atguigu.pojo"/>
    </typeAliases>
    ```
    

### 21.mapper标签

- 概述

    - 加载映射配置文件
- 代码
  
    ```xml
    <!--加载映射配置文件-->
    <mappers>
        <!--传统dao开发、接口代理开发-->
        <mapper resource="com/atguigu/dao/ExamDao.xml"/>
        <mapper resource="com/atguigu/dao/UserDao.xml"/>
    </mappers>
    ```
    
    ```xml
    <!--加载映射配置文件-->
    <mappers>
        <!--接口代理开发-->
        <mapper class="com.atguigu.dao.UserDao"></mapper>
        <mapper class="com.atguigu.dao.ExamDao"></mapper>
    </mappers>
    ```
    
    ```xml
    <mappers>
        <!--接口代理开发-->
        <package name="com.atguigu.dao"/>
    </mappers>
    ```
    

### 22.核心配置文件之plugins插件

- 概述

    - mybatis可以使用第三方的插件对功能进行扩展，分页助手PageHelper是将分页的复杂操作进行封装，使用简单的方式即可获得分页的相关数据
- 开发步骤
    - ①引入pagehelper相关依赖
    - ②在mybatis-config.xml中配置pagehelper
    - ③定义service接口机器实现子类
        - 编写分页代码
    - ④定义dao接口及其实现子类
- ①引入pagehelper相关依赖
  
    ```xml
    <dependency>
        <groupId>com.github.pagehelper</groupId>
        <artifactId>pagehelper</artifactId>
        <version>5.2.1</version>
    </dependency>
    ```
    
- ②在mybatis-config.xml中配置pagehelper
  
    ```xml
    <plugins>
        <plugin interceptor="com.github.pagehelper.PageInterceptor">
            <property name="reasonable" value="true"/>
        </plugin>
    </plugins>
    ```
    
- ③定义service接口及其实现子类
  
    ```java
    @Override
    public PageInfo<Exam> selectExamListByPage(Integer currentPage, Integer pageSize) throws Exception {
        //开启分页查询
        PageHelper.startPage(currentPage, pageSize);
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder()
                .build(Resources.getResourceAsStream("SqlMapConfig.xml"));
        SqlSession sqlSession = sqlSessionFactory.openSession();
        ExamDao examDao = sqlSession.getMapper(ExamDao.class);
        List<Exam> examList = examDao.selectExamList();
    
        sqlSession.close();
        return new PageInfo<>(examList);
    }
    ```
    
- ④定义dao接口及其实现子类
- ⑤代码测试
  
    ```java
    @Test
    public void selectExamListByPage() throws Exception {
        ExamService examService = new ExamServiceImpl();
        PageInfo<Exam> pageInfo = examService.selectExamListByPage(2, 2);
        System.out.println("pageInfo = " + pageInfo);
        System.out.println("当前页数:"+pageInfo.getPageNum());
        System.out.println("每页记录数:"+pageInfo.getPageSize());
        System.out.println("总页数:"+pageInfo.getPages());
        System.out.println("上一页的页码:"+pageInfo.getPrePage());
        System.out.println("下一页的页码:"+pageInfo.getNextPage());
        System.out.println("是否是第一页:"+pageInfo.isIsFirstPage());
        System.out.println("是否是最后一页:"+pageInfo.isIsLastPage());
        System.out.println("总记录数:"+pageInfo.getTotal());
        System.out.println("当前页数据:"+pageInfo.getList());
    }
    ```
    

### 23.给SQL语句传参

- 概述

    - Mybatis会在运行过程中，把配置文件中的SQL语句中的#{}转换为”?”占位符，发送给数据库执行。
- 需求

    - 根据账户模糊查询
- 代码实现
  
    ```java
    <select id="selectUserLikeName1" parameterType="string" resultType="user">
        select *
        from tb_user
        where user_name like "%"#{userName}"%"
    </select>
    
    <select id="selectUserLikeName2" parameterType="string" resultType="user">
        select *
        from tb_user
        where user_name like "%${userName}%"
    </select>
    ```
    
- 总结
    - #{}
        - 不能直接写入字符串中
        - 可以防止SQL注入漏洞
        - 不可以传入表名
    - ${}
        - 能直接写入到字符串中
        - 不可以防止SQL注入漏洞
        - 可以传入表名

### 24.输入映射

- 概述
    - 输入映射：是在映射文件中通过parameterType指定输入参数的类型。从而将用户输入的内容赋值到SQL语句的占位符中。
- 简单类型：包含一个值
    - 基本数据类型
    - 包装类
    - String
- 复杂类型：包含多个值
    - javabean实体类
    - 集合
    - 数组

### 25.输入映射之简单类型

- 概述

    - 输入一个简单类型的值
- 需求

    - 根据id查询用户记录
- 代码实现
  
    ```xml
    <select id="selectUserById1" parameterType="int" resultType="user">
        select *
        from tb_user
        where user_id = #{userId}
    </select>
    
    <select id="selectUserById2"  resultType="user">
        select *
        from tb_user
        where user_id = #{userId}
    </select>
    ```
    

### 26.输入映射之零散简单类型

- 概述

    - 输入多个简单类型的值
- 需求

    - 根据账户和密码查询记录
- 代码实现
  
    ```xml
    <select id="selectUserByNameAndPwd1" resultType="user">
        select *
        from tb_user
        where user_name = #{param1}
          and user_pwd = #{param2}
    </select>
    
    <select id="selectUserByNameAndPwd2" resultType="user">
        select *
        from tb_user
        where user_name = #{userName}
          and user_pwd = #{userPwd}
    </select>
    ```
    
    ```java
    User selectUserByNameAndPwd1(String userName , String userPwd) throws Exception;
    
    User selectUserByNameAndPwd2(@Param("userName") String userName , @Param("userPwd") String userPwd) throws Exception;
    ```
    

### 27.输入映射之实体类型

- 概述

    - mybatis会根据#{}中传入的数据，加工成getXxx()方法，通过反射在实体类对象中调用这个方法，从而获取到对应的数据。填充到#{}这个位置
- 需求

    - 根据用户名和密码查询用户
- 代码实现
  
    ```java
    <select id="selectUserByNameAndPwd3" parameterType="user" resultType="user">
        select *
        from tb_user
        where user_name = #{userName}
          and user_pwd = #{userPwd}
    </select>
    
    <select id="selectUserByNameAndPwd4"  resultType="user">
        select *
        from tb_user
        where user_name = #{userName}
          and user_pwd = #{userPwd}
    </select>
    ```
    

### 28.输入映射之Map

- 概述

    - 如果要输入多个简单类型的值，同时也找不合适的javabean来进行封装，使用零散简单类型(@Param)又太过复杂，此时就可以使用map封装，然后进行输入映射
- 代码实现
  
    ```xml
    <select id="selectUserByMap" parameterType="map"  resultType="user">
        select *
        from tb_user
        where user_name = #{userName}
          and user_pwd = #{userPwd}
    </select>
    
    <select id="selectUserByMap2"   resultType="user">
        select *
        from tb_user
        where user_name = #{userName}
          and user_pwd = #{userPwd}
    </select>
    ```
    
- 注意事项
    - #{userName}和map的key一致
    - parameterType=”map”可以省略

### 29.输出映射

- 概述
    - 输出映射，是在映射文件中通过resultType指定输出结果的类型。类型可以是简单类型、复杂类型
- 简单类型：包含一个值
    - 基本数据类型
    - 包装类
    - String

### 30.输出映射值之简单类型

- 概述

    - 输出一个简单类型的结果
- 需求

    - 查询总记录数
- 代码实现
  
    ```xml
    <select id="selectTotalCount" resultType="long">
        select count(*)
        from tb_user
    </select>
    ```
    

### 31.输出映射之Map

- 概述
    - 适用于SQL查询返回的各个字段综合起来并不和一个现有的实体类对应，没法封装到实体类对象中。能够封装成实体类型的，就不使用map类型
    - map的键存储的是字段的名，map的值存储的是字段的值
- 代码实现
  
    ```xml
    <select id="selectUserById4" resultType="map">
        select *
        from tb_user
        where user_id = #{userId}
    </select>
    ```
    

### 32. 输出映射之List

- 概述

    - 查询结果返回多个实体类对象，希望把多个实体类对象放在List集合中返回。此时不需要任何特殊处理，还是在resultType属性中设置实体类类型
- 代码实现
  
    ```xml
    <select id="selectUserList" resultType="user">
        select *
        from tb_user
    </select>
    ```
    

### 33.输出映射之ResultMap

- 概述
    - 如果数据库中的字段名称(a_column)和实体类中的属性名称(aColumn)不一致，会导致输出映射不成功！
    - 可以通过设置别名、mapUnderscoreToCamelCase=true、resultMap来解决
    - resultMap自定义映射规则来达到输出映射的目的
- 代码实现
  
    ```xml
    <!--自定义输出映射规则-->
    <resultMap id="userMap" type="user">
        <id column="user_id" property="userId" javaType="Integer"></id>
        <result column="user_name" property="userName" javaType="String"></result>
        <result column="user_pwd" property="userPwd" javaType="String"></result>
        <result column="money" property="money" javaType="Double"></result>
    </resultMap>
    
    <select id="selectUserListByResultMap" resultMap="userMap">
        select *
        from tb_user
    </select>
    ```
    
- 注意事项

    - 如果表中的字段名和类的属性名一致，就不需要在resultMap中自定义映射规则，可以自动输出映射

### 34.insert实现主键返回

- 概述

    - 添加记录后，获取已添加的记录的自增长主键值

- 分类
    - ①使用useGenerateKeys属性
    - ②使用selectKey标签

- ①使用useGenerateKeys属性

    ```xml
    <insert id="addUser1" useGeneratedKeys="true" keyColumn="user_id" keyProperty="userId">
        insert into tb_user
        values (null, #{userName}, #{userPwd}, #{money})
    </insert>
    ```

- ②使用selectKey标签

    ```xml
    <insert id="addUser2" >
        insert into tb_user
        values (null, #{userName}, #{userPwd}, #{money})
        <selectKey keyColumn="user_id" keyProperty="userId" resultType="int"  order="AFTER">
            select last_insert_id()
        </selectKey>
    </insert>
    ```

### 35.动态SQL之SQL片段

* 概述

  * 在开发中，SQL的拼接很常见，有很多的sql具有重复性高的特点，这时最好把重复的sql抽取出来，作为公用的sql片段。

* 代码实现

  ```xml
  <!--定义SQL片段-->
  <sql id="queryArgs">
      user_name , user_pwd
  </sql>
  
  <select id="selectUserLikeName1" parameterType="string" resultType="user">
      select
      /*使用SQL片段*/
      <include refid="queryArgs"></include>
      from tb_user
      where user_name like "%"#{userName}"%"
  </select>
  ```

### 36.动态SQL之if标签

* 概述

  * if标签可以根据条件动态地生成SQL语句

* 需求

  * 如果账户和密码都没有就查询所有用户记录
  * 如果只有账户，就根据账户查询用户记录
  * 如果只有密码，就根据密码查询用户记录
  * 如果账户和密码都有，就根据账户和密码查询用户

* 代码实现

  ```xml
  <!--存在问题，SQL语句一成不变，无法达到需求-->
  <select id="selectUserListByNameAndPwd" resultType="user">
      select *
      from tb_user
      where user_name = #{userName}
      and user_pwd = #{userPwd}
  </select>
  ```

  ```xml
  <select id="selectUserListByNameAndPwd2" resultType="user">
      select *
      from tb_user
      where 1 = 1
      <if test="userName!=null and userName!=''">
          and user_name = #{userName}
      </if>
      <if test="userPwd!=null and userPwd!=''">
          and user_Pwd = #{userPwd}
      </if>
  </select>
  ```

* 存在问题

  * 不够优雅~~~

### 37.动态SQL之where标签

* 概述

  * where标签会自动去掉标签体内前面多余的and/or

* 代码实现

  ```xml
  <select id="selectUserListByWhere" resultType="user">
      select *
      from tb_user
      <where>
          <if test="userName!=null and userName!=''">
              and user_name = #{userName}
          </if>
          <if test="userPwd!=null and userPwd!=''">
              and user_Pwd = #{userPwd}
          </if>
      </where>
  </select>
  ```

### 38.动态SQL之trim标签

* 常用属性

  | 属性            | 说明                       |
  | --------------- | -------------------------- |
  | prefix          | 如果条件成立，生成指定前缀 |
  | prefixOverrides | 如果条件成立，移除指定前缀 |
  | suffix          | 如果条件成立，生成指定后缀 |
  | suffixOverrides | 如果条件成立，移除指定后缀 |

 * 代码实现

   ```xml
   <select id="selectUserListByTrim" resultType="user">
       select *
       from tb_user
       /*
           根据条件，动态地生成where关键字，动态地移除and或or，相当于where标签
       */
       <trim prefix="where" prefixOverrides="and|or">
           <if test="userName!=null and userName!=''">
               and user_name = #{userName}
           </if>
           <if test="userPwd!=null and userPwd!=''">
               and user_Pwd = #{userPwd}
           </if>
       </trim>
   </select>
   ```

### 39.动态SQL之set标签

* 概述

  * 动态生成set关键字，会自动去掉标签体内后面多余的逗号

* 需求

  * 根据id修改用户信息

* 代码实现

  ```xml
  <update id="updateUserById">
      update tb_user
      set
      <if test="userName!=null and userName!=''">
          user_name = #{userName},
      </if>
      <if test="userPwd!=null and userPwd!=''">
          user_pwd = #{userPwd},
      </if>
      where user_id = #{userId}
  </update>
  ```

  ```xml
  <update id="updateUserById">
      update tb_user
      <set>
          <if test="userName!=null and userName!=''">
              user_name = #{userName},
          </if>
          <if test="userPwd!=null and userPwd!=''">
              user_pwd = #{userPwd},
          </if>
      </set>
  
      where user_id = #{userId}
  </update>
  ```

### 40.动态SQL之choose标签

* 概述

  * 在多个分支条件中，仅执行一个，而后面的条件匹配都会被忽略

* 代码实现

  ```xml
  <select id="selectUserListByChoose" resultType="user">
      select * from tb_user
      <where>
          <choose>
              <when test="userName!=null and userName!=''">
                  user_name = #{userName}
              </when>
              <when test="userPwd!=null and userPwd!=''">
                  user_pwd = #{userPwd}
              </when>
          </choose>
      </where>
  </select>
  ```

### 41.动态SQL之批量查询

* 需求

  * 根据一组id查询多条用户记录

* 代码实现

  ```xml
  <!--
      方式一：select * from tb_user where user_id in(1,2,3)
  -->
  <select id="selectUserListByIds" resultType="user">
      select * from tb_user
      <where>
          user_id in
          <foreach collection="list" open="(" separator="," close=")" item="id">
              #{id}
          </foreach>
      </where>
  </select>
  
  <!--
      方式二：select * from tb_user where user_id = 1 or user_id = 2 or user_id = 3
  -->
  <select id="selectUserListByIds2" resultType="user">
      select * from tb_user
      <where>
          <foreach collection="list" separator="or" item="id">
              user_id = #{id}
          </foreach>
      </where>
  </select>
  ```

### 42.动态SQL之批量添加

* 需求

  * 批量添加用户记录

* 代码实现

  ```xml
  <insert id="addUsers">
      insert into tb_user values
      <foreach collection="list" separator="," item="user">
          (null,#{user.userName},#{user.userPwd},#{user.money})
      </foreach>
  </insert>
  ```

### 43.动态SQL之批量修改

* 需求

  * 批量修改用户记录

* 代码实现

  ```properties
  driverClass=com.mysql.jdbc.Driver
  url=jdbc:mysql://localhost:3306/mydb1?allowMultiQueries=true
  username=root
  password=root
  ```

  ```xml
  <update id="updateUsers">
      <foreach collection="list" separator=";" item="user">
          update tb_user set user_name = #{user.userName} where user_id = #{user.userId}
      </foreach>
  </update>
  ```

### 44.多表关系设计回顾

* 一对一：表A中的一条记录确定表B中的一条记录，表B中的一条记录确定表A中的一条记录
  * 比如：公司和注册地址
  * 一对一查询
* 一对多：表A中的一条记录确定表B中的多条记录，表B中的一条记录确定表A中的一条记录
  * 比如：部门和员工
  * 一对一查询
  * 一对多查询
* 多对多：表A中的一条记录确定表B中的多条记录，表B中的一条记录确定表A中的多条记录
  * 比如：老师和学生
  * 一对多查询
  * 一对多查询
* 

