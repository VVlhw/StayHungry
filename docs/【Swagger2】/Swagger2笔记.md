# Swagger2笔记

## 使用

- 依赖：`Springfox-swagger2`、`swagger-springmvc`

- 新建配置类，加`@EnableSwagger2`。

  ```java
  @Configuration //配置类
  @EnableSwagger2// 开启Swagger2的自动配置
  public class SwaggerConfig {
      @Bean
      public Docket docket() {
          return new Docket(DocumentationType.SWAGGER_2)
              .apiInfo(apiInfo())
              .enable(true) //用于开启Swagger 
              .select()      
              .apis(RequestHandlerSelectors
                    .basePackage("com.kuang.swagger.controller"))
              .paths() //用于过滤
              .build();
      }
    // 用下面的自己配置的
    // 通过.select()方法，去配置扫描接口,RequestHandlerSelectors配置如何扫描接口
      
    //配置文档信息
      private ApiInfo apiInfo() {
          Contact contact = new Contact("联系人名字", "http://xxx.xxx.com/联系人访问链接", "联系人邮箱");
          return new ApiInfo(
              "Swagger学习", // 标题
              "学习演示如何配置Swagger", // 描述
              "v1.0", // 版本
              "http://terms.service.url/组织链接", // 组织链接
              contact, // 联系人信息
              "Apach 2.0 许可", // 许可
              "许可链接", // 许可连接
              new ArrayList<>()// 扩展
          );
      }
  }
  ```
  
- 访问：http://localhost:项目端口/swagger-ui.html
  
- 注解

  - `@ApiModel`为类添加注释
  - `@ApiModelProperty`为类属性添加注释
  - `@ApiOperation`("xxx接口说明")
  - `@ApiParam`("xxx参数说明")：在参数、方法和字段上
  - `@Api`(tags = "xxx模块说明")：在模块类上
  - 对应Controller用具体的@xxxMapping

## 扩展



> 如何动态配置当项目处于test、dev环境时显示swagger，处于prod时不显示？
> A：通过Environment

```java
@Bean
public Docket docket(Environment environment) {
    // 设置要显示swagger的环境
    Profiles of = Profiles.of("dev", "test");
    // 判断当前是否处于该环境
    // 通过 enable() 接收此参数判断是否要显示
    boolean b = environment.acceptsProfiles(of);

    return new Docket(DocumentationType.SWAGGER_2)
        .apiInfo(apiInfo())
        .enable(b) //通过一个b变量
        .select()
        .apis(RequestHandlerSelectors
              .basePackage("com.kuang.swagger.controller"))
        .paths(PathSelectors.ant("/kuang/**"))
        .build();
}
```

> ==一个Docket实例对应一个后台程序员==
> 配置分组groupName，对应那个Docket实例

```java
@Bean
public Docket docket1(){
    return new Docket(DocumentationType.SWAGGER_2).groupName("group1");
}
@Bean
public Docket docket2(){
    return new Docket(DocumentationType.SWAGGER_2).groupName("group2");
}
```

> 皮肤，选择以下一款依赖

- springfox-swagger-ui

- swagger-bootstrap-ui

- swagger-ui-layer

- swagger-mg-ui

  

## 资料

狂神说Swagger2文章：https://mp.weixin.qq.com/s/0-c0MAgtyOeKx6qzmdUG0w

狂神讲解的配套视频地址：https://www.bilibili.com/video/BV1Y441197Lw