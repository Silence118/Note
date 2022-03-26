# 一、Swagger2介绍

前后端分离开发模式中，api文档是最好的沟通方式。

Swagger 是一个规范和完整的框架，用于生成、描述、调用和可视化 RESTful 风格的 Web 服务。

1. 及时性 (接口变更后，能够及时准确地通知相关前后端开发人员)
2. 规范性 (并且保证接口的规范性，如接口的地址，请求方式，参数及响应格式和错误信息)
3. 一致性 (接口信息一致，不会出现因开发人员拿到的文档版本不一致，而出现分歧)
4. 可测性 (直接在接口文档上进行测试，以方便理解业务)

# 二、配置Swagger2

## 1、创建common模块

**在yygh_parent下创建模块common**

配置：

groupId：com.atguigu

artifactId：common

![](assets/25314fcd-309e-4396-88d3-8f8e69ab5e78-20220306151045-7yvyh2g.png)

注意：common模块的src目录用不到可以删除!

## 2、在common中引入相关依赖

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
        <scope>provided </scope>
    </dependency>

    <!--mybatis-plus-->
    <dependency>
        <groupId>com.baomidou</groupId>
        <artifactId>mybatis-plus-boot-starter</artifactId>
        <scope>provided </scope>
    </dependency>

    <!--lombok用来简化实体类：需要安装lombok插件-->
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
    </dependency>

    <!--swagger-->
    <dependency>
        <groupId>io.springfox</groupId>
        <artifactId>springfox-swagger2</artifactId>
    </dependency>
    <dependency>
        <groupId>io.springfox</groupId>
        <artifactId>springfox-swagger-ui</artifactId>
    </dependency>

    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>fastjson</artifactId>
    </dependency>
</dependencies>
```

## 3、在common下面创建子模块service_utils

![](assets/73e4a0be-72b8-4ba2-9f15-76a0246c3ffa-20220306151045-sox3x1o.png)

## 4、在模块service-base中，创建swagger的配置类

创建包com.atguigu.yygh.common.config，创建类Swagger2Config

```java
@Configuration
@EnableSwagger2
public class Swagger2Config {
    @Bean
    public Docket webApiConfig(){
        return new Docket(DocumentationType.SWAGGER_2)
                .groupName("webApi")
                .apiInfo(webApiInfo())
                .select()
                //只显示api路径下的页面
                //.paths(Predicates.and(PathSelectors.regex("/api/.*")))
                .build();
    }

    @Bean
    public Docket adminApiConfig(){
        return new Docket(DocumentationType.SWAGGER_2)
                .groupName("adminApi")
                .apiInfo(adminApiInfo())
                .select()
                //只显示admin路径下的页面
                .paths(Predicates.and(PathSelectors.regex("/admin/.*")))
                .build();
    }

    private ApiInfo webApiInfo(){
        return new ApiInfoBuilder()
                .title("网站-API文档")
                .description("本文档描述了网站微服务接口定义")
                .version("1.0")
                .contact(new Contact("atguigu", "http://atguigu.com", "493211102@qq.com"))
                .build();
    }

    private ApiInfo adminApiInfo(){
        return new ApiInfoBuilder()
                .title("后台管理系统-API文档")
                .description("本文档描述了后台管理系统微服务接口定义")
                .version("1.0")
                .contact(new Contact("atguigu", "http://atguigu.com", "49321112@qq.com"))
                .build();
    }
}
```

## 5、在模块service模块中引入service_utils

```xml
<dependency>
    <groupId>com.atguigu</groupId>
    <artifactId>service_utils</artifactId>
    <version>0.0.1-SNAPSHOT</version>
</dependency>
```

## 6、在service_hosp启动类上添加注解，进行测试

![](assets/a401e219-a181-4c7f-8292-5ed11e800073-20220306151045-ffo7zt2.png)

## 7、通过地址访问测试

![](assets/edd3f7a4-0038-4638-99d7-18ef4cdc8657-20220306151045-0oxhrxq.png)

可以添加一些自定义设置，例如：

定义样例数据

```java
@ApiModelProperty(value = "创建时间", example = "2019-01-01 8:00:00")
@TableField(fill = FieldFill.INSERT)
private Date gmtCreate;

@ApiModelProperty(value = "更新时间", example = "2019-01-01 8:00:00")
@TableField(fill = FieldFill.INSERT_UPDATE)
private Date gmtModified;
```

![](assets/9c17851c-8e7e-4161-968c-18b36faf148a-20220306151045-jhgn12t.png)

## 8、定义接口说明和参数说明

定义在方法上：@ApiOperation

定义在参数上：@ApiParam

```java
//医院设置接口
@Api(description = "医院设置接口")
@RestController
@RequestMapping("/admin/hosp/hospitalSet")
public class HospitalSetController {

    @Autowired
    private HospitalSetService hospitalSetService;

    //查询所有医院设置
    @ApiOperation(value = "医院设置列表")
    @GetMapping("findAll")
    public List<HospitalSet> findAll() {
        List<HospitalSet> list = hospitalSetService.list();
        return list;
    }

    @ApiOperation(value = "医院设置删除")
    @DeleteMapping("{id}")
    public boolean removeById(@ApiParam(name = "id", value = "医院设置ID", required = true) @PathVariable String id){
        return hospitalSetService.removeById(id);
    }
}
```

![](assets/0392ad2f-c440-4977-b2af-ebdad37b6686-20220306151045-jmybrnt.png)
