# 1. FSM
三段式状态机：
- 状态寄存器（时序逻辑）
    → 保存当前状态 
- 次态组合逻辑
    → 根据 当前状态 + 输入 决定下一个状态
- 输出逻辑
    → 根据状态（Moore）或状态+输入（Mealy）产生输出

# 2. scala
- val / var
	```scala
	var a = "fds" // var 可重新赋值
	val a = "fds" // val 不可重新赋值
	```
- List
	```scala
	val list = List(1, 2, 3)
	val list = a :: b :: c :: Nil
	val list3 = list1 ++ list2
	val m = list2.length
	val s = list2.size
	val headOfList = list1.head // 第一个元素
	val restOfList = list1.tail // 除第一个元素的list
	val third = list1(2) // 第三个元素
	```
- if
	```scala
	// if 赋值
	val likelyCharactersSet = if (alphabet.length == 26)
	    "english"
	else 
	    "not english"
	```
- for
	```scala
	for (i <- 0 to 7) { print(i + " ") } // 包括 7
	for (i <- 0 until 7) { print(i + " ") } // 不包括 7
	for(i <- 0 to 10 by 2) { print(i + " ") } // 添加步长
	for (value <- list) { } // 遍历 list
	
	```
- Map / Option
	```scala
	// 字典
	val map = Map("a" -> 1) 
	val a = map.get("a")
	
	// Option 两个 subclass：Some / None
	val some = Some(1)
	val none = None
	println(some.get)
	println(none.getOrElse(2)) // Returns 2
	
	```
- 函数
	- 重载：重写参数列表
	- 最后一行为返回值
	- 支持内嵌函数

# 3. chisel
## 3.1. General
- `:=`：信号驱动，最后一次连接有效
- 编写 module
	```scala
	class Passthrough extends Module {
	val io = IO(new Bundle {
		val in = Input(UInt(4.W))
		val out = Output(UInt(4.W))
      })
    io.out := io.in
    }
	```
- print
	- println：硬件生成时期打印，生成 verilog 时触发
	- printf：仿真时打印，每个时钟周期触发
	```scala
	printf("%d", io.in) // 传统方法，打印数值
	printf(p"$io") // chisel 可以直接展开打印整个 Bundle
	```
- Mux / Cat
	```scala
		// Mux 实现二选一，true 选 num1
		io.out_mux := Mux(bool, num1, num2)
		
		// Cat 拼接两个数字（b10, b1 -> b101）
		io.out_cat := Cat(num1, num2) 
	```
- 加法
	- `+`：普通加法，溢出后发生截断
	- `+&`：扩展加法，溢出后自动增加位宽
- when / elsewhen / otherwise
	- when 块**不会返回**任何值
	- 会综合成多路选择器
	```scala
	when(bool) {
		...
	}.elsewhen(bool) {
		...
	}.otherwise {
		...
	}
	```
- 比较
	- `==`：scala 的对象比较
	- `===`：chisel 生成比较电路
- Reg
	```scala
	//  普通寄存器
	val register = Reg(UInt(12.W))
	//  创建一个寄存器，并将输入信号延迟一个周期，第二位为初始值
	io.out := RegNext(io.in + 1.U, 0.U)
	//  初始化的寄存器
	val myReg = RegInit(UInt(12.W), 0.U)
	val myReg = RegInit(0.U(12.W))
	```

## 3.2. Test
```scala
poke(<signal>) // 注入信号
c.clock.step(n) // 时钟走 n 步
expect(<signal>) // 验证信号是否符合预期

// 对于 Queue
val in = IO(Flipped(Decoupled(ioType)))
val out = IO(Decoupled(ioType))
out <> Queue(in, entries)
=== 复位
//对 in / out 时钟信号复位
c.in.initSource() 
c.in.setSourceClock(c.clock)
=== 单个元素测试
c.in.enqueueNow(<item>)  // 压入元素
c.out.expectDequeueNow(<item>)  // 测试出队列元素
=== 用 Seq 测试
val testVector = Seq.tabulate(100){ i => i.U }
c.in.enqueueSeq(testVector)  // 将所有 Seq 的内容压入
c.out.expectDequeueSeq(testVector)  // 挨个弹出 Queue 中的内容，并与 Seq 中的内容比较
```


## 3.3. Clock
```scala
//  自定义复位
withReset(io.alternateReset) {
	... // 内部所有的硬件的复位端均会连接到 alternateReset
}

//  自定义时钟
withClock(io.alternateClock) {
	... //  内部的硬件都由 alternateClock 驱动
}

//  同时自定义复位和时钟
withClockAndReset(io.alternateClock, io.alternateReset) {
	...
}
```

## 3.4. 参数化
- Arguments
```scala
// 通过 Option 参数化（以免用 -1 等判断）
class DelayBy1(resetValue: Option[UInt] = None) extends Module {
	val reg = if (resetValue.isDefined) { 
        ...
    } else {
        ...
    }
}

// Value Matching
val y = 7
val x = y match {
  case 0 => "zero" 
  case 1 =>    
      "one"      
  case 2 => {    
      "two"
  }
  case _ => "many" 
}

// Multiple Value Matching
def animalType(biggerThanBreadBox: Boolean, meanAsCanBe: Boolean): String = {
  (biggerThanBreadBox, meanAsCanBe) match {
    case (true, true) => "wolverine"
    case (true, false) => "elephant"
    case (false, true) => "shrew"
    case (false, false) => "puppy"
  }
}

// Type Matching
val sequence = Seq("a", 1, 0.0)
sequence.foreach { x =>
  x match {
    case s: String => println(s"$x is a String")
    case s: Int    => println(s"$x is an Int")
    case s: Double => println(s"$x is a Double")
    case _ => println(s"$x is an unknown type!")
  }
}

// Multiple Type Matching
sequence.foreach { x =>
  x match {
    case _: Int | _: Double => println(s"$x is a number!")
    case _ => println(s"$x is an unknown type!")
  }
}

// Type Erasure
// Scala 是跑在 JVM 上，在运行时会进行类型擦除
// Seq[String] 与 Seq[Int]类型均会视作 Seq 类型，无其它区别
val sequence = Seq(Seq("a"), Seq(1), Seq(0.0))
sequence.foreach { x =>
  x match {
    case s: Seq[String] => println(s"$x is a String")
    case s: Seq[Int]    => println(s"$x is an Int")
    case s: Seq[Double] => println(s"$x is a Double")
  }
}

```
- IO
```scala
// 使用 getOrElse()
val carryIn = if (hasCarry) Some(Input(UInt(1.W))) else None
val sum = io.a +& io.b +& io.carryIn.getOrElse(0.U)

// 使用 0 宽度 IO，会在生成 Verilog 时被消除
// 可以避免信号值 0 有用的情况造成歧义
val carryIn = Input(if (hasCarry) UInt(1.W) else UInt(0.W))

```
- Implicit: 在函数深处访问某个顶层变量
	- 缺失的 implicit 型变量会在范围内寻找匹配的类型值
	- 两种失败情况：
		- 找不到匹配的值
		- 有多个匹配的值
```scala
// Implicit Arguments
object CatDog {
  implicit val numberOfCats: Int = 3

  def tooManyCats(nDogs: Int)(implicit nCats: Int): Boolean = nCats > nDogs
    
  val imp = tooManyCats(2)    // Argument passed implicitly!
  val exp = tooManyCats(2)(1) // Argument passed explicitly!
}

// Implicit Conversions
class Animal(val name: String, val species: String)
class Human(val name: String)
implicit def human2animal(h: Human): Animal = new Animal(h.name, "Homo sapiens")
val me = new Human("Adam")
println(me.species)
```
