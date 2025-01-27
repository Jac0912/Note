# Gatter&Setter
```java
@Data  //加在类上，自动生成Gatter和Setter方法

@Getter  //加在类或字段上，boolean的Gatter命名为[ is<属性> ]
@Getter(AccessLevel.<访问权限>)  //生成特定访问权限的Getter（PUBLIC、PACKAGE、PRIVATE、PROTECTED、MODULE、NONE(不生产Getter)）
@Getter(onMethod_ = {<其它注解>})  //添加额外注解
@Getter(lazy = true)  //实行懒初始化（get时才初始化）

@Setter  //加在类或字段上

@Tolerate  //加在方法上，如果手动写get或set方法Lombok会则不会生成对应属性的get或set方法，通过该注解可让Lombok正常生成
```
# 构造方法
```java
@AllArgsConstructor  //加在类上，生成全参构造方法
@AllArgsConstructor(staticName = "<名字>")  //加在类上，生成全参静态构造方法（通过<类名>.<名字>构造对象）
@NoArgsConstructor  //加在类上，生成无参构造方法
@RequiredArgsConstructor  //加在类上，对必要参数生成构造方法（如final字段的方法）
```
# ToString
```java
@ToString  //加在类上，自动重写toString方法
@ToString(includeFieldNames = false)  //加在类上，生成的toString只打印值
@ToString(doNotUseGetters = true)  //加在类上，生成toString时尽量不适用Getter
@ToString(onlyExplictlyIncluded = true)  //加在类上，开启白名单模式
@ToString.Include  //加在属性上，在白名单模式下使用，包含某个属性；加在方法上，将方法作为属性打印
@ToString.Include(rank = <数字>)  //决定打印的顺序
@ToString.Include(name = "<别名>")  //加在属性上，toString时使用别名
```
# equal与hashcode
```java
@EqualsAndHashCode  //添加equals与hashcode
@EqualsAndHashCode(onlyExplicitlyIncluded = true) //白名单模式
@EqualsAndHashCode(cacheStrategy = EqualsAndHashCode.CacheStrategy.LAZY) //懒加载hashcode(缓存hashcode，避免重复计算，针对属性不会变化的情景！！)

@EqualsAndHashCode.Exclude  //加在属性上，比较时忽略该属性
@EqualsAndHashCode.Include  //加在属性上，比较时考虑该属性（在白名单模式下使用）
@EqualsAndHashCode.Include(replaces = "<属性>")  //加在方法上，自定义对<属性>的比较
```
# 判空
```java
@NonNull  //添加在方法参数前，自动对参数进行判空处理
```