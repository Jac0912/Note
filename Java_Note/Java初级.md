# 面向过程

- **对象**：对象是类的一个实例，有状态和行为。例如，一条狗是一个对象，它的状态有：颜色、名字、品种；行为有：摇尾巴、叫、吃等。
- **类**：类是一个模板，它描述一类对象的行为和状态。
- **方法**：方法就是行为，一个类可以有很多方法。逻辑运算、数据修改以及所有动作都是在方法中完成的。
- **实例变量**：每个对象都有独特的实例变量，对象的状态由这些实例变量的值决定。

## 变量

- 不能重复定义变量
- 不能以数字开头
- final相当于const
- Java**没有**无符号数
- 小的类型可赋值给大类型（隐式转换），**反之不可**      byte<short<int<long

 ![image-20240115194856430](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240115194856430.png)

可用下划线分割位数：1_000_000

十六进制：$0x$开头

八进制：0开头

2.3默认为double类型

float f=3.8f;

**隐式转换**：byte→short(char)→int→long→float→double

### 字符

String不是基本数据类型

### 布尔类型

boolean

### 字符串

可用加号进行拼接

**是一种变量**（与c区分）

## 运算符

赋值运算后结果为本身

![image-20240115222625573](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240115222625573.png) 

![image-20240115223212856](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240115223212856.png)

**注意：**

![image-20240115223328237](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240115223328237.png)

**结果：**30Hello

## 位运算

### &（与）

两个数每一位都比较，都是1结果就为1，否则为0

#### |（或）

两个数每一位都比较，有1为1，全0为0

### ^(异或)

两个数每一位都比较，不同为1，相同为0

### ~（取反）

对一个数操作，0变1，1变0

### <<和>>(位移运算符)

![image-20240115231418969](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240115231418969.png)

**符号位不受位移操作的影响**

![image-20240115231755987](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240115231755987.png)

**>>>与<<<**会连同符号一起移动

![image-20240115233545833](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240115233545833.png)

## 流程控制

**continue、break:**后面可加标签，对相应代码块操作

---

---

# 面向对象

![image-20240115190538757](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240115190538757.png)

## 创建对象

对象是根据类创建的。在Java中，使用关键字 new 来创建一个新的对象。创建对象需要以下三步：

- **声明**：声明一个对象，包括对象名称和对象类型。
- **实例化**：使用**关键字 new** 来创建一个对象。
- **初始化**：使用 new 创建对象时，会调用构造方法初始化对象。（类似于c中结构体）

```java
public class Person
{
    String name;
    int age;
    String sex;
    "方法"
}
```



![image-20240116101658301](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240116101658301.png)

person相当于指向新对象的指针

![image-20240116102217414](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240116102217414.png)

#### 初始值：

数字变量：0

字符串变量：null

布尔类型：false

## 方法（c中函数）

对象的行为

基本变量类型的参数传递只是**值传递**

![image-20240116112939117](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240116112939117.png)

p属于应用类型的变量，存放的是对象的应用，不是对象的本身，值传递相当于将对象的引用复制到方法内部的变量中，内部的变量依然是引用同一个对象（**p相当于指针！！！**）

![image-20240116113816295](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240116113816295.png)

重名优先使用离作用域最近的变量

![image-20240116114010392](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240116114010392.png)

 **this：**访问当前对象的属性

### 方法的重载

![image-20240116114824659](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240116114824659.png)

**不允许仅返回值类型不同**

###  构造方法

![image-20240116120558873](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240116120558873.png)

构造方法后会覆盖掉原有默认的无参构造

构造方法也可以重载

![image-20240116120936123](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240116120936123.png)

---

可以构建对象时就赋初始值（手动初始化）：

![image-20240116121312588](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240116121312588.png)

调用无参构造的方法后会赋初始值

----

可以在类中添加代码块，代码块在对象构造之前成员变量初始化之前执行（标准情况下通过构造方法进行对象初始化）

## 静态变量和静态方法

![image-20240116151049679](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240116151049679.png)

**static：**所有对象共享，可直接通过类名进行操作             ($Person.info=“Test"$)（静态方法同理）

![image-20240116153159187](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240116153159187.png)

静态方法：

- 无法获取成员变量值
- 也无法调用非静态方法
- 无法使用**this**关键字（this代表的是当前对象本身）
- 调用时不需要对象
- 可以访问静态变量（都属于类）

也可以将代码块变为静态

![image-20240116154219743](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240116154219743.png)

被标记为静态的内容会在类刚加载时就分配，而不是在对象创建时分配（静态内容在第一个对象初始化之前完成加载）

![image-20240116154906410](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240116154906410.png)

**Person person=new Person() 结果：**

![image-20240116154733172](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240116154733172.png)

## 包和访问控制

### 包

对类进行分类

**import:**导入不是同一个包下的类

![image-20240116173503977](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240116173503977.png)

有歧义时的引用可以通过补全引用来消除：

![image-20240116174118975](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240116174118975.png)

### 访问权限控制

![image-20240116175558266](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240116175558266.png)

类不能是**private、protected**的权限，private创造出的类无意义

![image-20240116180457996](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240116180457996.png)

## 封装、继承、多态

### 类的封装

![image-20240116181452734](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240116181452734.png)

可进行其他操作：![image-20240116181735967](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240116181735967.png)

（如果包含”小“就无法修改）![image-20240116182048774](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240116182048774.png)

![image-20240116182121400](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240116182121400.png)

封装思想就是把实现细节隐藏，外部只需知道方法的作用，无需关心实现，要用什么由类自己来做，不需要外面来操作类内部（由权限控制实现）

#### 类的继承

![image-20240116183006475](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240116183006475.png)

可以不断往下继承，属性会被继承，可以直接访问父类中定义的属性，除非父类中的权限为private（但依然继承了此属性）

如果父类的构造方法为有参构造，需要用**supper**关键字先完成父类的构造

![image-20240116184633621](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240116184633621.png)

子类构造可不需要参数：![image-20240116185037104](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240116185037104.png)

**不能在构造父类构造方法之前进行其他活动**

![image-20240116185321674](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240116185321674.png)

使用子类时可以当成父类使用：

![image-20240116185858237](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240116185858237.png)

并不代表子类转变为父类，是仅当做父类使用

----

可强制转换（只适用于对象本身就是对应的子类）

![image-20240116190439791](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240116190439791.png)

![image-20240116190626767](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240116190626767.png)

---

关键词**instanceof**可判断对象是对应类型或是对应类型的子类

![image-20240116190948233](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240116190948233.png)

---

子类可以定义与父类同名的属性（使用时就近原则）

![image-20240116191508093](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240116191508093.png)

存在同名变量情况下可用**supper**关键字表示父类：

![image-20240116191813520](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240116191813520.png)

**注：**没有supper.supper的用法

### 顶层的Object类

所有类都默认继承自**Object**类

### 方法的重写

与方法的重载不同，重写会覆盖原有方法

返回值与形参同原方法相同，重写内容

优先使用重写后的方法

可以改权限，**只能向高改**，如果子类无法访问此方法，再次书写相当于创建了新方法

静态方法不支持重写，它属于类本身，但是可以被继承

-----

基于重写的特性，对于一个类定义的行为，不同的子类可以出现不同的行为，对同一个方法产生不同的结果

![image-20240116195400531](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240116195400531.png)

![image-20240116195525939](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240116195525939.png)

**是面向对象多态特性的一种体现**

----

不希望重写某个方法可在方法前加**final**关键字，表示此方法为最终形态，但可以重载

![image-20240116195857741](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240116195857741.png)

类上加**final**，此类不能再继承

属性加**final**，此属性只能赋一次值

---

用**supper**可调用父类中原本方法的实现

![image-20240116200723171](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240116200723171.png)![image-20240116200734512](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240116200734512.png)

### 抽象类

越是顶层的类越可以抽象

顶层类只保留方法主体，具体方法体实现由子类实现

用关键字**abstract**实现，如果是抽象方法，子类必须实现（除非子类也为抽象类）

![image-20240116202128331](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240116202128331.png)

**抽象类也无法调用构造方法进行实体化**

![image-20240116202532865](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240116202532865.png)

只能使用子类![image-20240116202621782](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240116202621782.png)

抽象类只用作继承，抽象类的子类也可以是抽象类

抽象类可以有其他实体方法

抽象类的访问权限不能是**private**

## 从控制台中读取数据

1. 导包： import java.util.Scanner
2. 对象：创建Scanner对象     Scanner pig=new Scanner(System.in);
3. 调用Scanner类中的方法（nextXxx）
4. 关闭资源，调用Scanner类中的close（）

**注：**只能获取byte,short,int,long,float,double,boolean,String(**无char**)

**char：**next().CharAt(0)

树状数组、模拟、线段树、分块、珂朵莉树



## Set

**存储不重复元素**

```java
Set<String> set = new HashSet<>();
add(E e);
clear();
contains(Object o);
equals(Object o);
isEmpty();
remove(Object o);
size();
toArray();
```

## List

**动态数组**

```java
List<Integer>list=new ArraysList<>();
add(Object element);
size();
get(int index);

set(int index,Integer element)
//list内的值不可直接修改，需调用set函数赋值

//不常用
isEmpty();
contains(Object o);    
remove(int index);
```

## Map

```java
HashMap(字典)
Map<Integer,Integer>map=new HashMap<>();
put(K key,V value);
get(Object key);
size();
getOrderfault(K key,Object value);   //获取key对应value，如果找不到，返回默认值
```

## 栈

```java
Stack<Integer> stack=new Stack<>();
push(Object element);
pop();
peek();
isEmpty();
```

**先进后出**

## 队列

```java
Queue<Integer>queue=new LinkedList<>();
add(Object element);
poll();                                //返回、删除队头元素
peek();                                //返回队头元素
isEmpty();
```

**先进先出**

### 优先队列

```java
PriorityQueue<Integer>Que = new PriorityQueue<>();
PriorityQueue<Integer>Que = new PriorityQueue<>(Comparator.reverseOrder());   //降序

priorityQueue.offer(num);  //num:数字
priorityQueue.poll();
```



## 排序库

### 数组

```java
//一维
Arrays.sort(int[]a);
Arrays.sort(Integer[]a,(o1,o2)->o1-o2);   //升序
Arrays.sort(Integer[]a,(o1,o2)->o2-o1);   //降序

//二维
Arrays.sort(Integer[]a,(o1,o2)->o1[0]-o2[0]);
```

### 集合

```java 
Collections.sort(List<>arr);
Collections.sort(List<>arr,(o1,o2)->o1-o2);   //升序
Collections.sort(List<>arr,(o1,o2)->o2-o1);   //降序
```

## 字符串

### String

```java
charAt(int index);                        //返回指定索引字符
compareTo(Object o);                      //比较字符串
equals(String str);
indexOf(String str);                      //字串第一次出现的索引
substring(int beginIndex,int endIndex);   //返回截取的字符串[beginIndex,endIndex)
split(String regex);                 	  //分割为字符串数组
toCharArray(String str);
length();
isEmpty();
```

`int a,char a`⇒`String`

String out=a+“”;

### StringBuilder

```java
StringBuilder str=new StringBuilder();
StringBuilder str=new StringBuilder(String str);
append(Object obj);
indexOf(String str);           //字串第一次出现的索引
reverse();                     //反转字符串
isEmpty();
charAt(int index);
insert(int index,char x);      //字符插入指定位置
```

**ep:**

```java
        String temp="jgifs";
		char[] cover=temp.toCharArray();
		cover.toString();
		StringBuilder test=new StringBuilder(temp);
```

## Character

```java
isLetter(char ch);
isDigit(char ch);
isLowerCase(char ch);
isSpaceChar(char ch);
isUpperCase(char ch);
isWhitespace(char ch);
```

## LocalDate

```java
LocalDate date = LocalDate.now(); // 获取当前日期
LocalDate specificDate = LocalDate.of(2022, 3, 14); // 使用指定的年、月、日创建日期
LocalDate parsedDate = LocalDate.parse("2022-03-14"); // 从字符串解析日期
LocalDate modifiedDate = date.plusDays(5); // 加上指定的天数
LocalDate previousMonth = date.minusMonths(1); // 减去一个月
boolean isLeapYear = date.isLeapYear(); // 判断是否为闰年
```



## 时间、空间复杂度

![image-20240218221307401](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240218221307401.png)

![image-20240218221418710](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240218221418710.png)

![image-20240218221450178](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240218221450178.png)

