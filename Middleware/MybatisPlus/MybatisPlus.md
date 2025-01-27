# 三层架构
实体类中：
```java
@Entity  //打在实体类上
@Tablename("<表名>")  //打在实体类上

@TableId(type = IdType.AUTO)  //打在id属性上
@TableField("<列名>")  //打在属性上，进行列映射
```

在Mapper接口中：
```java
@Mapper
public interface <实体类名>Mapper extends BaseMapper<<类名>>{
  //仍然可用注解自定义方法
}
```

在查询方法中：
```java
@Resource
<实体类名>Mapper mapper;
mapper.<方法>;

//自定义查询条件(写在方法中)
QueryWrapper<<实体类名>> wrapper = new QueryWrapper<>();
wrapper.<查询条件方法>;  //可写成方法链，见官网
```

# 直接与Service层交互
service包下的接口中(<实体类名>Service)：
```java
public interface <实体类名>Service extends IService<<实体类类名>> {

}
```

service/impl包下写实现类(<实体类名>ServiceImpl)：
```java
@Service
public class <实体类名>ServiceImpl extends ServiceImpl<<实体类名>Mapper, <实体类名>> implements <实体类名>Service{

}
```

直接调用业务层：
```java
@Resource
<实体类名>Service service;

service.<链式查询方法>;
```

# 代码生成器
代码生成脚本：
```java
@Resource
DataSource source;
FastAutoGenerator.create(new DataSourceConfig.Builder(source))
                 .globalConfig(builder -> {
	                 builder.outputDir("src/main/java")  //输出目录设置为当前目录
                 })
                 .packageConfig(builder -> builder.parent("<生成代码的包名>"))
                 .strategyConfig(builder -> {
	                 builder
			                .mapperBuilder()
			                .mapperAnnotation(Mapper.class)
			                .build();
                 })
                 .execute();
```