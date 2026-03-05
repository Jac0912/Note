# 1. 语法
## 1.1. 数值
- 电平：
	- 0：假
	- 1：真
	- x：未知
	- z：高阻
- 整数数值：
	- 十进制('d 或 'D)
	- 十六进制('h 或 'H)
	- 二进制（'b 或 'B）
	- 八进制（'o 或 'O）

## 1.2. 数据类型
- wire：硬件之间的物理连线，由连接器件输出端连续驱动
- 寄存器类型：存储单元
	- reg：无符号数
	- integer（32bit）：有符号数
	- real：浮点数
	- time（64bit）：保存仿真时间
- 向量：
```verilog
// 向量声明
reg [3:0]      counter ;    //声明4bit位宽的寄存器counter
wire [32-1:0]  gpio_data;   //声明32bit位宽的线型变量gpio_data
wire [8:2]     addr ;       //声明7bit位宽的线型变量addr，位宽范围为8:2
reg [0:31]     data ;       //声明32bit位宽的寄存器变量data, 最高有效位为0

// 向量访问
// [bit+: width]: 从起始 bit 位开始递增，位宽为 width
// [bit-: width]: 从起始 bit 位开始递减，位宽为 width
A = data1[31-: 8] ;  
A = data1[31:24] ; // 等效
B = data1[0+ : 8] ;  
B = data1[0:7] ; // 等效

// 数据拼接
wire [31:0]    temp1, temp2 ;  
assign temp1 = {byte1[0][7:0], data1[31:8]};  //数据拼接  
assign temp2 = {32{1'b0}};  //赋值32位的数值0
```
- 数组
```verilog
reg  [3:0]       counter [3:0] ; //由4个4bit计数器组成的数组  
wire [7:0]       addr_bus [3:0] ; //由4个8bit wire型变量组成的数组  
wire             data_bit[7:0][5:0] ; //声明1bit wire型变量的二维数组
```
- parameter：参数（常量，只能赋值一次）
- 字符串：保存在 reg 中，一个字符占一个字节（8bit），若没占满则用 0 补空位

## 1.3. 表达式
- 等价操作符：
	- 逻辑等（`==`）：包含 x 或 z 时，结果为不确定
	- 全等（`===`）：包含相同的 x 或 z 时，返回结果是 1
- 按位操作：如果位宽不够用 0 向左扩展操作数
- 归约操作符：归约与（&），归约与非（~&），归约或（|），归约或非（~|），归约异或（^），归约同或（~^）
- 移位操作符：
	- 左移（<<），右移（>>）：符号位也动
	- 算术左移（<<<），算术右移（>>>）：符号位不动
- 条件操作符：可用作选择器
```verilog
assign   hsel = (addr[9:8] == 2'b00) ? hsel_p1 : 
                (addr[9:8] == 2'b01) ? hsel_p2 :
                (addr[9:8] == 2'b10) ? hsel_p3 :
                (addr[9:8] == 2'b11) ? hsel_p4 ;
```

## 1.4. 编译指令
```verilog
// 文本替换
`define 

// 取消之前的宏定义
`undef

// 条件编译指令
`ifdef, `ifndef, `elsif, `else, `endif

// 将一个 Verilog 文件嵌入另一个文件中（C 中 #include）
`include

// 仿真单位和精度
// 遇到另一个 `timescale 或 `resetall更新时延
// 如果有多个 `timescale，单位不变，精度取最精细
`timescale      time_unit / time_precision
1000：s（秒）→ ms（毫秒）→ us（微妙）→ ns（纳秒）→ ps（皮秒）→ fs（飞秒）

// 声明隐式线网类型
`default_nettype none

// 将模块标记为单元模块
`celldefine, `endcelldefine
``` 

# 2. 赋值
## 2.1. 连续赋值
```verilog
// assign， 用于对 wire 赋值
// LHS_target 必须是一个标量或者线型向量，而不能是寄存器类型
// RHS_expression 的类型没有要求，可以是标量或线型或存器向量，也可以是函数调用
// 只要 RHS_expression 表达式的操作数有事件发生（值的变化）时，RHS_expression 就会立刻重新计算，同时赋值给 LHS_target
assign     LHS_target = RHS_expression  ；
```
## 2.2. 过程赋值
- 赋值对象：寄存器、实数、整数等类型
- 赋值后值保持不变直到被重新赋值
- 两类：
	- 阻塞赋值（=）：顺序执行，下一条语句执行前当前语句执行完
	- 非阻塞赋值（<=）：并行执行
- 不混合使用阻塞和非阻塞赋值，时序不易控制
	- 设计电路时：always 时序逻辑块多用非阻塞赋值，always 组合逻辑块中多用阻塞赋值
	- 仿真电路时：initial 中多使用阻塞赋值
## 2.3. 过程连续赋值
- 是过程赋值的一种
- 过程连续赋值的表达式能被连续驱动到 wire 或 reg 型变量中
- assign, deassign：复制对象只能是寄存器或寄存器组，寄存器中的值被保留直到被重新赋值
- force，release：赋值对象可以是 reg 或 wire
	- reg：force 作用时，寄存器当前值覆盖；release 时寄存器值保留强制赋值的值，之后可以被原有语句改变
	- wire：release 时**立马**变成原有的驱动值
# 3. 时序
## 3.1. 时延
- 用于控制操作数发生变化到赋予左端新值时间延时
- 以下均为**惯性时延**：在时延期间如果 A&B 发生变化，Z 拿到的是最新的值
```verilog
//普通时延
wire Z, A, B ;  
assign #10    Z = A & B ;

//隐式时延
wire A, B;  
wire #10        Z = A & B;

//声明时延，声明一个wire型变量是指定一个时延。因此对该变量所有的连续赋值都会被推迟到指定的时间。除非门级建模中，一般不推荐使用此类方法建模。  
wire A, B;  
wire #10 Z ;  
assign           Z =A & B
```
## 3.2. 时序控制
- 时延控制
	- 常规时延：延迟结束后，查看 RHS_expression 的值，然后赋值
	- 内嵌时延：先保存 RHS_expression 的值，然后等延时结束后赋值
- 边沿触发事件控制
	- 一般事件控制
		- `@`：事件控制符号
		- `posedge`：信号边沿正向跳变
		- `negedge`：信号边沿负向跳变
		- 未指明：两种边沿变化都触发
	- 命名事件控制：定义 event 类型的变量，通过触发该变量识别事件是否发生
	- 敏感列表：多个信号或事件来触发语句的执行
- 电平敏感事件控制：等某个条件为真再执行后续语句
```verilog
// 常规时延
reg  value_test ;
reg  value_general ;
#10  value_general    = value_test ;

// 内嵌时延
reg  value_test ;
reg  value_embed ;
value_embed        = #10 value_test ;


//信号clk只要发生变化，就执行q<=d，双边沿D触发器模型  
always @(clk) q <= d ;                  
//在信号clk上升沿时刻，执行q<=d，正边沿D触发器模型  
always @(posedge clk) q <= d ;    
//在信号clk下降沿时刻，执行q<=d，负边沿D触发器模型  
always @(negedge clk) q <= d ;  
//立刻计算d的值，并在clk上升沿时刻赋值给q，不推荐这种写法  
q = @(posedge clk) d ;


// 命名事件控制
event     start_receiving ;  
always @( posedge clk_samp) begin  
        -> start_receiving ;       //采样时钟上升沿作为时间触发时刻  
end  
   
always @(start_receiving) begin  
    data_buf = {data_if[0], data_if[1]} ; //触发时刻，对多维数据整合  
end


// 敏感列表
//带有低有效复位端的D触发器模型
always @(posedge clk or negedge rstn)    begin      
//always @(posedge clk , negedge rstn)    begin      
//也可以使用逗号陈列多个事件触发
    if（! rstn）begin
        q <= 1'b ;      
    end
    else begin
        q <= d ;
    end
end
// 可以通过 @* 或 @(*) 来简写，表示对所有语句块的输入变量变化敏感
always @(*) begin  
//always @(a, b, c, d, e, f, g, h, i, j, k, l, m) begin  
//两种写法等价  
    assign s = a? b+c : d ? e+f : g ? h+i : j ? k+l : m ;  
end


// 电平敏感事件控制
initial begin  
    wait (start_enable) ;      //等待 start 信号  
    forever begin  
        //start信号使能后，在clk_samp上升沿，对数据进行整合  
        @(posedge clk_samp)  ;  
        data_buf = {data_if[0], data_if[1]} ;        
    end  
end
```
# 4. 语句
## 4.1. 过程结构
- 多个 initial 与 always 块并行执行
- 但是块内允许执行
```verilog
// initial
// 不可综合
// 用来初始化、信号检测
initial begin
	...
end

// always
// 重复执行
// 仿真时钟，信号行为检测
always begin
	...
end
```
## 4.2. 语句块
- 顺序块：begin / end
- 并行块：fork / join，即使阻塞赋值也是同时执行
- 嵌套块：嵌套顺序块和并行块
- 命名块：给块语句结构命名，通过层次名引用的方式对局部变量访问
	- 也可通过  `disable` 来终止命名块的执行，不同于 C 中的 break 只能退出当前循环，disable 可以禁用设计中任何一个命名的块
- 条件语句：多路选择器
```verilog
if (condition1)       true_statement1 ;
else if (condition2)        true_statement2 ;
else if (condition3)        true_statement3 ;
else                      default_statement ;
```
## 4.3. 多路分支语句
- case 语句
	- `case_expr` 可以是 x / z（但是无法综合，不建议使用）
	- 有多个选项执行相同的语句时，可以用 `,` 隔开，放在一个 `case_expr` 中
```verilog
case(case_expr)
    condition1     :             true_statement1 ;
    condition2     :             true_statement2 ;
    ...
    default        :             default_statement ;
endcase
```
- casex / casez 语句（不可综合，用来仿真）
	- casex 用 "x" 来表示无关值，casez 用问号 "?" 来表示无关值
## 4.4. 循环语句
- while
- for（无 `i++` 或 `i--`，需写成 `i = i + 1`）
- repeat：循环次数固定，如果循环次数是变量，选用进入循环时变量的值为循环次数，后续不改变
- forever：永久循环，用 `$finish` 退出 forever

# 5. 模块
- 端口
- 变量（parameter / reg / wire）
- 数据流（assign）
- 底层模块例化
- 行为级语句（initial / always）
- 任务、函数
## 5.1. 端口
- 输入（input）、输出（output）、双向端口（inout）
- input / inout 不能声明为 reg（reg 是用来保存数值的，输入端口只能反映外部信号的变化）
- 端口隐式声明为 wire，只有需要 reg 时需要声明
```verilog
module pad(  
    input        DIN, OEN ,  
    input [1:0]  PULL ,  
    inout        PAD ,  
    output reg   DOUT  
    );
```
## 5.2. 模块例化
- 在一个模块中引用另一个模块，对其端口进行相关连接
- 命名端口连接：需要例化的模块与外部信号按名字进行连接，output 端口可以悬空不连甚至删除
```verilog
//output 端口 Co 悬空
full_adder1  u_adder0(
    .Ai     (a[0]),
    .Bi     (b[0]),
    .Ci     (c==1'b1 ? 1'b0 : 1'b1),
    .So     (so_bit0),
    .Co     ());
 
//output 端口 Co 删除
full_adder1  u_adder0(
    .Ai     (a[0]),
    .Bi     (b[0]),
    .Ci     (c==1'b1 ? 1'b0 : 1'b1),
    .So     (so_bit0));
```
- 顺序端口连接：需要与外部信号顺序保持一致
```verilog
full_adder1  u_adder1(
    a[1], b[1], co_temp[0], so_bit1, co_temp[1]);
```
- 用 generate 例化：例化多个相同的模块
```verilog
module full_adder4(
    input [3:0]   a ,   //adder1
    input [3:0]   b ,   //adder2
    input         c ,   //input carry bit
 
    output [3:0]  so ,  //adding result
    output        co    //output carry bit
    );
 
    wire [3:0]    co_temp ; 
    //第一个例化模块一般格式有所差异，需要单独例化
    full_adder1  u_adder0(
        .Ai     (a[0]),
        .Bi     (b[0]),
        .Ci     (c==1'b1 ? 1'b1 : 1'b0),
        .So     (so[0]),
        .Co     (co_temp[0]));
 
    genvar        i ;
    generate
        for(i=1; i<=3; i=i+1) begin: adder_gen
        full_adder1  u_adder(
            .Ai     (a[i]),
            .Bi     (b[i]),
            .Ci     (co_temp[i-1]), //上一个全加器的溢位是下一个的进位
            .So     (so[i]),
            .Co     (co_temp[i]));
        end
    endgenerate
 
    assign co    = co_temp[3] ;
 
endmodule
```
- 层次访问：用 `.` 操作来访问具体的标识符
## 5.3. 带参数例化
- defparam 语句
```verilog
//instantiation
defparam     u_ram_4x4.MASK = 7 ;
ram_4x4    u_ram_4x4
    (
        .CLK    (clk),
        .A      (a[4-1:0]),
        .D      (d),
        .EN     (en),
        .WR     (wr),    //1 for write and 0 for read
        .Q      (q)    );
```
- 带参数模块例化
```verilog
ram #(.AW(4), .DW(4))
    u_ram
    (
        .CLK    (clk),
        .A      (a[AW-1:0]),
        .D      (d),
        .EN     (en),
        .WR     (wr),    //1 for write and 0 for read
        .Q      (q)
     );
```
# 6. 函数
- 只能在模块中定义，作用范围局限于模块
- 不含有任何延迟、时序或时序控制逻辑
- 至少有一个输入变量
- 只有一个返回值，且没有输出
- 不含有非阻塞赋值语句
- 函数可以调用其他函数，但是不能调用任务
```verilog
// 会隐式声明一个宽度为 range、名字为 function_id 的寄存器变量，返回值通过变量传递
function [range-1:0]     function_id ;
 input_declaration ;
 other_declaration ;
procedural_statement ;
endfunction
```
- 常数函数
	- 编译期间计算结果为常数的函数
	- 不能访问全局变量或调用系统函数
	- 可以调用另一个常数函数
- automatic 函数
	- 一般函数的局部变量是静态的：函数的每次调用局部变量使用同一个存储空间
	- 如果一个函数在不同的地方同时调用，会对同一块地址进行操作
	- 用 automatic 来说明可以重新分配新的内存空间
	- 局部变量不能通过层次名访问，函数本身可以通过层次名来调用

# 7. 任务
- 在函数的基础上可以包含时序控制逻辑
- 没有输入变量
- 没有输出或输出端的数量大于 1

| 比较点  | 函数                         | 任务                                 |
| ---- | -------------------------- | ---------------------------------- |
| 输入   | 函数至少有一个输入，端口声明不能包含 inout 型 | 任务可以没有或者有多个输入，且端口声明可以为 inout 型     |
| 输出   | 函数没有输出                     | 任务可以没有或者有多个输出                      |
| 返回值  | 函数至少有一个返回值                 | 任务没有返回值                            |
| 仿真时刻 | 函数总在零时刻就开始执行               | 任务可以在非零时刻执行                        |
| 时序逻辑 | 函数不能包含任何时序控制逻辑             | 任务不能出现 always 语句，但可以包含其他时序控制，如延时语句 |
| 调用   | 函数只能调用函数，不能调用任务            | 任务可以调用函数和任务                        |
| 书写规范 | 函数不能单独作为一条语句出现，只能放在赋值语言的右端 | 任务可以作为一条单独的语句出现语句块中                |

```verilog
task       task_id ;
    port_declaration ;
    procedural_statement ;
endtask

```
- input 看作 wire，也可以是 reg
- output 看作 reg，不需要特殊说明 reg（强制是 reg）
- output 赋值不使用 assign，建议采用阻塞赋值
- 任务可以看作是过程性赋值，如果要观察变量的操作过程，需要将变量声明在模块之内、任务之外
- automatic 任务：用于多个相同任务并发执行