实体类中：
```java
@Entity  //打在实体类上
@Table(name = "<表名>")  //打在实体类上

@Id  //打在id属性上
@GeneratedValue(strategy = GenerationType.IDENTITY)  //打在id属性上，表明自增

@Column(name = "<列名>")  //打在属性上，进行列映射

@OneToOne  //一对一表关联
@OneToOne(fetch = FetchType.LAZY)  //一对一表关联，并实行懒加载(查询方法上需加 @Transactional (在事务模式下使用))
@OneToOne(cascade = CascadeType.<操作>)  //对选择操作进行关联操作(ALL: 所有操作，PERSIST: 插入，REMOVE: 删除，MERGE:修改)
@JoinColumn(name = "<本表列名，与关联表中的id对应>")
<详细信息类> detail;  //两张表关联查询，调用查询方法时自动查询外表
```

application.yml中：
```yaml
jpa:
 show-sql: true
 hibernate:
  ddl-auto: none
```

repo(包名)/<实体类名>Repository(接口)
```java
@Repository
public interface <实体类名>Repository extends JpaRepository<<实体类名>, Integer>{
	<实体类名> find<实体类名>By<属性>Like(String str);  //模糊查询(可选)，会自动实现
	List<实体类名> find<实体类名>sBy<属性>GreaterThanOrderByIdDesc(int i);  //查询所有id大于i并降序排序(可选)，会自动实现
}
```
通过方法名拼接来进行查询：

| 属性                | 拼接方法名称示例                                                    | 执行的语句                                                        |
| ----------------- | ----------------------------------------------------------- | ------------------------------------------------------------ |
| Distinct          | findDistinctByLastnameAndFirstname                          | select distinct … where x.lastname = ?1 and x.firstname = ?2 |
| And               | findByLastnameAndFirstname                                  | … where x.lastname = ?1 and x.firstname = ?2                 |
| Or                | findByLastnameOrFirstname                                   | … where x.lastname = ?1 or x.firstname = ?2                  |
| Is，Equals         | findByFirstname`,`findByFirstnameIs`,`findByFirstnameEquals | … where x.firstname = ?1                                     |
| Between           | findByStartDateBetween                                      | … where x.startDate between ?1 and ?2                        |
| LessThan          | findByAgeLessThan                                           | … where x.age < ?1                                           |
| LessThanEqual     | findByAgeLessThanEqual                                      | … where x.age <= ?1                                          |
| GreaterThan       | findByAgeGreaterThan                                        | … where x.age > ?1                                           |
| GreaterThanEqual  | findByAgeGreaterThanEqual                                   | … where x.age >= ?1                                          |
| After             | findByStartDateAfter                                        | … where x.startDate > ?1                                     |
| Before            | findByStartDateBefore                                       | … where x.startDate < ?1                                     |
| IsNull，Null       | findByAge(Is)Null                                           | … where x.age is null                                        |
| IsNotNull，NotNull | findByAge(Is)NotNull                                        | … where x.age not null                                       |
| Like              | findByFirstnameLike                                         | … where x.firstname like ?1                                  |
| NotLike           | findByFirstnameNotLike                                      | … where x.firstname not like ?1                              |
| StartingWith      | findByFirstnameStartingWith                                 | … where x.firstname like ?1（参数与附加`%`绑定）                      |
| EndingWith        | findByFirstnameEndingWith                                   | … where x.firstname like ?1（参数与前缀`%`绑定）                      |
| Containing        | findByFirstnameContaining                                   | … where x.firstname like ?1（参数绑定以`%`包装）                      |
| OrderBy           | findByAgeOrderByLastnameDesc                                | … where x.age = ?1 order by x.lastname desc                  |
| Not               | findByLastnameNot                                           | … where x.lastname <> ?1                                     |
| In                | findByAgeIn(Collection<Age> ages)                           | … where x.age in ?1                                          |
| NotIn             | findByAgeNotIn(Collection<Age> ages)                        | … where x.age not in ?1                                      |
| True              | findByActiveTrue                                            | … where x.active = true                                      |
| False             | findByActiveFalse                                           | … where x.active = false                                     |
| IgnoreCase        | findByFirstnameIgnoreCase                                   | … where UPPER(x.firstname) = UPPER(?1)                       |

在查询方法中：
```java
@Resource
<实体类名>Repository repository;

repository.<方法>;  //包括上述自定义方法
//repository.count(): 计数
//repository.save(): 更新数据
```

在查询接口中使用JPQL自定义SQL语句：
```java
@Repository
public interface <实体类名>Repository extends JpaRepository<<实体类名>, Integer>{
	@Transactional
	@Modifying
	@Query("<sql查询语句>")  //非原生sql语句(加 nativeQuery 使用原生sql语句)
	<放回值> <方法名> (<方法参数>);
}
```