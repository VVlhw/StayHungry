# MyBatisPlus笔记

## 我觉得它的优势

- 从JDBC到MyBatis，再到MyBatisPlus，**基本的CRUD也不用写了**。因为有`BaseMapper<T>`
- **代码生成**，Entity、Controller、Service、Dao全生成，只关注业务，简直不要太爽
- **动态配置sql的**，查什么属性给什么
- 查询会自动加上**逻辑删除**条件
- QueryWrapper**做到复杂的条件查询**，也不用写sql，面向对象编程
- 还能**分析sql速度**，方便后期优化掉慢查询
- 内置**分页插件**，不再手写limit或者rowbound或者pageHelper插件
- 内置的**唯一ID生成器**（雪花算法），或其它主键策略



## 使用

- **依赖**：mysql-connector-java、lombok、mybatis-plus-boot-starter

  > 不用导mybatis依赖了，MP就是依赖它的

- **配置数据库信息**。username、password、url、driver-class-name

  > mysql 8 驱动不同com.mysql.cj.jdbc.Driver、需要增加时区的配置
  > jdbc:mysql://localhost:3306/数据库名?
  > useSSL=true&useUnicode=true&characterEncoding=utf-8&serverTimezone=GMT%2B8

- **创建entity对象**

- 创建Mapper接口也就是Dao接口，**继承BaseMapper<T>**

- 启动类，**加@MapperScan(“mapper包”)**

- 使用就是注入Mapper接口（已经继承了BaseMapper<T>，直接用）

  > 可能注入报红，因为Mapper接口没弄成Bean
  >
  > Mapper接口上加`@Repository `
  > @Repository、@Mapper的区别：https://blog.csdn.net/Xu_JL1997/article/details/90934359
  
- 推荐配置上日志，看MyBatisPlus的运行情况

  ```properties
  # 输出到控制台
  mybatis-plus.configuration.log-impl=org.apache.ibatis.logging.stdout.StdOutImpl
  ```

## 注解和相关插件

- ==配置主键生成策略==，若设none，得自己给id。在实体类上加`@TableId(type = IdType.xxx)`

  > 默认 IdType.ID_WORKER
  > 分布式系统唯一id的几种方法：https://www.cnblogs.com/haoxinyue/p/5208136.html
  > 常用就是`雪花算法`。为什么？因为他占空间少，还几乎全球唯一。

- 数据库字段更新了，一定马上**同步entity类**

- **每个表都要有创建时间、修改时间**（要自动填充的，而不是自己代码new Date写的，`@TableField(fill = FieldFill.xxx)`，==配置处理器重写方法告诉其填充什么==）

  ```java
  @Slf4j
  @Component // 一定不要忘记把处理器加到IOC容器中！
  public class MyMetaObjectHandler implements MetaObjectHandler {
      // 插入时的填充策略
      @Override
      public void insertFill(MetaObject metaObject) {
          log.info("start insert fill.....");
          // 对应哪些字段，自动填充什么东西，metaObject
          metaObject
              this.setFieldValByName("createTime",new Date(),metaObject);
          this.setFieldValByName("updateTime",new Date(),metaObject);
      }
      // 更新时的填充策略
      @Override
      public void updateFill(MetaObject metaObject) {
          log.info("start update fill.....");
          this.setFieldValByName("updateTime",new Date(),metaObject);
      }
  }
  ```
  
  还要有**逻辑删除**（`@TableLogic(与全局反的话，可指明规则)`）。REF《阿里开发手册》
  
  ```properties
  # 配置逻辑删除
  # 反过来的话，可以通过@TableLogic（）加参数配置
  mybatis-plus.global-config.db-config.logic-delete-value=1
  mybatis-plus.global-config.db-config.logic-not-delete-value=0
  ```
  
  ```java
  // 逻辑删除组件！
  @Bean
  public ISqlInjector sqlInjector() {
      return new LogicSqlInjector();
  }
  ```
  


- ==配置乐观锁插件==，数据库、entity类加一个version字段（加`@Version`）。

  > 不理解加version去了解下CAS，加版本号来保证线程安全

  ```java
  // 扫描我们的 mapper 文件夹
  @MapperScan("com.kuang.mapper")
  @EnableTransactionManagement
  @Configuration // 配置类
  public class MyBatisPlusConfig {
      // 注册乐观锁插件
      @Bean
      public OptimisticLockerInterceptor optimisticLockerInterceptor() {
          return new OptimisticLockerInterceptor();
      }
  }
  ```

- ==配置分页，加拦截器组件==。然后new Page加条件

  ```java
  // 分页插件
  @Bean
  public PaginationInterceptor paginationInterceptor() {
      return  new PaginationInterceptor();
  }
  ```
  
- ==性能测试插件==
  
  ```java
  @Bean
  @Profile({"dev","test"})// 设置 dev test 环境开启，保证我们的效率
  public PerformanceInterceptor performanceInterceptor() {
      PerformanceInterceptor performanceInterceptor = new
          PerformanceInterceptor();
      performanceInterceptor.setMaxTime(100); // ms设置sql执行的最大时间，如果超过了则不
      执行
          performanceInterceptor.setFormat(true); // 是否格式化代码
      return performanceInterceptor;
  }
  ```
  
-  代码生成配置参考官网

## 问题

想要自定义返回一些新的字段，可以用`selectMaps`方法，往Map放字段。

如果想空的也返回null，可以配置MP加`call-setters-on-nulls: true`

参考网址：https://blog.csdn.net/qq_40943363/article/details/86511770


## 资料

官网：https://mp.baomidou.com/

==狂神说B站视频==：https://www.bilibili.com/video/BV17E411N7KN?p=17

还有其配套的文档