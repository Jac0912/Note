# 异常处理

## 异常分类

异常对象都是派生于Throwable类的一个类实例（继承）

![image-20240125185237278](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240125185237278.png)

**非检查型异常：**

Error：系统错误和资源耗尽（情况少见）

RuntimeException（自身问题）：

- 错误强转类型
- 越界数组访问
- 访问null指针

**检查型异常：**

IOException:

- 试图越过文件末尾继续读取数据
- 试图打开不存在文件
- 试图根据指定字符串查找Class对象（但该类不存在）

## 声明

![image-20240125190524738](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240125190524738.png)

不必声明从Error、RuntimeException继承的对象

（超类不抛出异常，子类也不能抛出异常）

exp：

![image-20240125191018974](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240125191018974.png)

1. 找到合适异常类
2. 创建对象
3. 将对象抛出