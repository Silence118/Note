# 一、搭建model模块

## 1、在父工程yygh_parent下面创建模块model

**选择 maven类型，点击下一步**

![](assets/66507374-dc86-4fb9-80d8-c7edcb400830-20220306145540-7gmsx52.png)

**输入模块名称model，下一步完成创建**

![](assets/668d062b-1815-406d-b70b-c48143ade308-20220306145540-e73nwhw.png)

## 2、添加项目需要的依赖

```xml
 <dependencies>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>
        <!--mybatis-plus-->
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <scope>provided </scope>
        </dependency>
        <!--swagger-->
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger2</artifactId>
            <scope>provided </scope>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>easyexcel</artifactId>
            <scope>provided </scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-mongodb</artifactId>
            <scope>provided </scope>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <scope>provided </scope>
        </dependency>
    </dependencies>
```

## 3、复制项目实体类和VO类

![](assets/9bf7e8c0-16b9-4c6a-b0f2-217a354d5231-20220306145540-mtqcrnt.png)

![](assets/c35cab3f-d26b-4628-95e7-e06d04ecd839-20220306145540-u6b8sqh.png)
