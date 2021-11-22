# Go语言程序设计

## 一、简介

### 1.1 helloworld

```go
//HelloWorld.go
package main

import "fmt"

func main() {
	fmt.Println("Hello World")
}
```

### 1.2 编译

`go bulid xxx.go`

-   此命令可以创建go的可执行文件，可以直接在当前平台使用执行

### 1.3 执行

-   真实的生产环境为先编译，后直接执行可执行文件
-   `go run xxx.go`支持直接运行

### 1.4 非面向对象

-   若要使用对象，需要使用（结构体+方法构造）等其他方法实现
-   继承

```go
import "fmt"

type people struct {
	name string
	age int
}

type student struct {
	people
	school string
}

type programmer struct {
	people
	language string
}

func (p people) say() {
	fmt.Printf("我是%s，今年%d岁了，和我一起学习Go语言吧！\n", p.name, p.age)
}

func main() {
	stu := student{people{"行小观", 1}, "阳光小学"}
	stu.say()
	prom := programmer{people{"张三", 1}, "蓝天建筑有限公司"}
	prom.say()
}
```

-   重写

```go
type people struct {
	name string
	age int
}

type student struct {
	people
	school string
}

type programmer struct {
	people
	language string
}

func (p people) say() { //people的say方法
	fmt.Printf("我是%s，今年%d岁了，和我一起学习Go语言吧！\n", p.name, p.age)
}

func (s student) say() { //student重写people的say方法
	fmt.Printf("我是%s，是个学生，今年%d岁了，我在%s上学！\n", s.name, s.age, s.school)
}

func (p programmer) say() { //programmer重写people的say方法
	fmt.Printf("我是%s，是个程序员，今年%d岁了，我使用%s语言！\n", p.name, p.age, p.language)
}

func main() {
	stu := student{people{"李向前", 1}, "阳光小学"}
	stu.say()
	prmger := programmer{people{"张三", 1}, "Go"}
	prmger.say()
}
```

### 1.5 执行流程

## 二、基础语法

### 2.1 变量

-   `var xxx dataType`
-   `var xxx = data`
-   `xxx := data`
-   `var xxx dataType = data`

```go
//例
s := ""
var s = ""
var s string
var s string = ""
```

-   Go语言的null为nil
-   变量不能重复声明
    -   若已声明的变量使用`:=`会报错
-   多变量声明

```go
//类型相同多个变量, 非全局变量
var vname1, vname2, vname3 type
vname1, vname2, vname3 = v1, v2, v3

var vname1, vname2, vname3 = v1, v2, v3 // 和 python 很像,不需要显示声明类型，自动推断

vname1, vname2, vname3 := v1, v2, v3 // 出现在 := 左侧的变量不应该是已经被声明过的，否则会导致编译错误


// 这种因式分解关键字的写法一般用于声明全局变量
var (
    vname1 v_type1
    vname2 v_type2
)
```

#### 2.1.1 初始化

-   无值初始化

    -   以下几种为nil

        -   ```go
            var a *int
            var a []int
            var a map[string] int
            var a chan int
            var a func(string) int
            var a error // error 是接口
            ```

    -   `var a string`为""

    -   `var b int`为0

    -   `var c bool`为false

2.1.2 值类型和引用类型

-   值类型，如int、float、bool、string，使用这些类型的变量直接指向存在内存中的值：
    -   ![截屏2021-11-18 11.18.54](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-18%2011.18.54.png)
    -   使用`=`将一个变量的值赋给另一个变量，如i=j,实际上是在内存中把i的值进行了拷贝
        -   ![截屏2021-11-18 11.21.22](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-18%2011.21.22.png)
    -   可以通过&i来获取变量i的内存地址，即指针，指针实际上也被存在另外某一个值中。
        -   同一个引用类型的指针教育存在连续的内存地址中
        -   ![截屏2021-11-18 11.23.03](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-18%2011.23.03.png)

#### 2.1.2 作用域

-   函数内部定义的变量为局部变量
-   函数外定义的变量为全局变量
-   函数定义中的变量称为形式参数

#### 2.1.3 数组

```go
var variable_name [SIZE] variable_type
```

-   例

```go
var balance [10] float32
```

-   初始化数组

```go
var balance = [5]float32{1000.0, 2.0, 3.4, 7.0, 50.0}
```

```go
balance := [5]float32{1000.0, 2.0, 3.4, 7.0, 50.0}
```

-   可变长度数组定义

```go
var balance = [...]float32{1000.0, 2.0, 3.4, 7.0, 50.0}
或
balance := [...]float32{1000.0, 2.0, 3.4, 7.0, 50.0}
```

-   若确定了数组长度，可以对数组中某个索引位置数组赋值

```go
//  将索引为 1 和 3 的元素初始化
balance := [5]float32{1:2.0,3:7.0}
```

-   初始化数组中{}中的元素个数不能大于设置的[]中的数字
-   如果忽略[]中的数字而不设定数组大小，Go会根据元素个数设置数组大小
-   访问数组元素：通过索引

```go
var salary float32 = balance[9]
```

-   多维数组

```go
var variable_name [SIZE1][SIZE2]...[SIZEN] variable_type
```

-   例

```go
a := [3][4]int{  
 {0, 1, 2, 3} ,   /*  第一行索引为 0 */
 {4, 5, 6, 7} ,   /*  第二行索引为 1 */
 {8, 9, 10, 11},   /* 第三行索引为 2 */
}
```



### 2.2 常量

-   `const identifier [type] = value`

-   显式声明 `const b string = "abc"`

-   隐式声明`const b = "abc"`

-   多个相同类型`const a,b = a1,b1`

-   常量可以做枚举

    -   ```java
        const(
        	Unknown = 0
          Female = 1
          Male = 2
        )
        ```

-   iota 特殊常量

    -   在const中有规律变化

    -   ```go
        const (
                    a = iota   //0
                    b          //1
                    c          //2
                    d = "ha"   //独立值，iota += 1
                    e          //"ha"   iota += 1
                    f = 100    //iota +=1
                    g          //100  iota +=1
                    h = iota   //7,恢复计数
                    i          //8
            )
        ```

    -   ```go
        const (
            i=1<<iota			//1		i=1左移0位
            j=3<<iota			//6		j=3左移1位
            k							//12  k=3左移2位
            l							//24	l=3左移3位
        )
        ```



### 2.3 Go运算符

-   内置的运算符有：

>   算术运算符
>
>   关系运算符
>
>   逻辑运算符
>
>   位运算符
>
>   赋值运算符
>
>   其他

#### 2.3.1 算术运算符

-   A=10，B=20

| 运算符 | 描述 |        实例        |
| :----- | :--- | :----------------: |
| +      | 相加 | A + B 输出结果 30  |
| -      | 相减 | A - B 输出结果 -10 |
| *      | 相乘 | A * B 输出结果 200 |
| /      | 相除 |  B / A 输出结果 2  |
| %      | 求余 |  B % A 输出结果 0  |
| ++     | 自增 |  A++ 输出结果 11   |
| --     | 自减 |   A-- 输出结果 9   |

#### 2.3.2 关系运算符

-   A=10，B=20

| 运算符 | 描述                                                         | 实例              |
| :----- | :----------------------------------------------------------- | :---------------- |
| ==     | 检查两个值是否相等，如果相等返回 True 否则返回 False。       | (A == B) 为 False |
| !=     | 检查两个值是否不相等，如果不相等返回 True 否则返回 False。   | (A != B) 为 True  |
| >      | 检查左边值是否大于右边值，如果是返回 True 否则返回 False。   | (A > B) 为 False  |
| <      | 检查左边值是否小于右边值，如果是返回 True 否则返回 False。   | (A < B) 为 True   |
| >=     | 检查左边值是否大于等于右边值，如果是返回 True 否则返回 False。 | (A >= B) 为 False |
| <=     | 检查左边值是否小于等于右边值，如果是返回 True 否则返回 False。 | (A <= B) 为 True  |

#### 2.3.2 逻辑运算符

-   A=true,B=false

| 运算符 | 描述                                                         | 实例               |
| :----- | :----------------------------------------------------------- | :----------------- |
| &&     | 逻辑 AND 运算符。 如果两边的操作数都是 True，则条件 True，否则为 False。 | (A && B) 为 False  |
| \|\|   | 逻辑 OR 运算符。 如果两边的操作数有一个 True，则条件 True，否则为 False。 | (A \|\| B) 为 True |
| !      | 逻辑 NOT 运算符。 如果条件为 True，则逻辑 NOT 条件 False，否则为 True。 | !(A && B) 为 True  |

#### 2.3.3 位运算符

-   A=60,B=13

| p    | q    | p & q | p \| q | p ^ q |
| :--- | :--- | :---- | :----- | :---- |
| 0    | 0    | 0     | 0      | 0     |
| 0    | 1    | 0     | 1      | 1     |
| 1    | 1    | 1     | 1      | 0     |
| 1    | 0    | 0     | 1      | 1     |

| 运算符 | 描述                                                         | 实例                                   |
| :----- | :----------------------------------------------------------- | :------------------------------------- |
| &      | 按位与运算符"&"是双目运算符。 其功能是参与运算的两数各对应的二进位相与。 | (A & B) 结果为 12, 二进制为 0000 1100  |
| \|     | 按位或运算符"\|"是双目运算符。 其功能是参与运算的两数各对应的二进位相或 | (A \| B) 结果为 61, 二进制为 0011 1101 |
| ^      | 按位异或运算符"^"是双目运算符。 其功能是参与运算的两数各对应的二进位相异或，当两对应的二进位相异时，结果为1。 | (A ^ B) 结果为 49, 二进制为 0011 0001  |
| <<     | 左移运算符"<<"是双目运算符。左移n位就是乘以2的n次方。 其功能把"<<"左边的运算数的各二进位全部左移若干位，由"<<"右边的数指定移动的位数，高位丢弃，低位补0。 | A << 2 结果为 240 ，二进制为 1111 0000 |
| >>     | 右移运算符">>"是双目运算符。右移n位就是除以2的n次方。 其功能是把">>"左边的运算数的各二进位全部右移若干位，">>"右边的数指定移动的位数。 | A >> 2 结果为 15 ，二进制为 0000 1111  |

#### 2.3.4 赋值运算符

| 运算符 | 描述                                           | 实例                                  |
| :----- | :--------------------------------------------- | :------------------------------------ |
| =      | 简单的赋值运算符，将一个表达式的值赋给一个左值 | C = A + B 将 A + B 表达式结果赋值给 C |
| +=     | 相加后再赋值                                   | C += A 等于 C = C + A                 |
| -=     | 相减后再赋值                                   | C -= A 等于 C = C - A                 |
| *=     | 相乘后再赋值                                   | C *= A 等于 C = C * A                 |
| /=     | 相除后再赋值                                   | C /= A 等于 C = C / A                 |
| %=     | 求余后再赋值                                   | C %= A 等于 C = C % A                 |
| <<=    | 左移后赋值                                     | C <<= 2 等于 C = C << 2               |
| >>=    | 右移后赋值                                     | C >>= 2 等于 C = C >> 2               |
| &=     | 按位与后赋值                                   | C &= 2 等于 C = C & 2                 |
| ^=     | 按位异或后赋值                                 | C ^= 2 等于 C = C ^ 2                 |
| \|=    | 按位或后赋值                                   | C \|= 2 等于 C = C \| 2               |

#### 2.3.5 其他运算符

| 运算符 | 描述             | 实例                       |
| :----- | :--------------- | :------------------------- |
| &      | 返回变量存储地址 | &a; 将给出变量的实际地址。 |
| *      | 指针变量。       | *a; 是一个指针变量         |

#### 2.3.6 运算符优先级

-   数字越大优先级越高

| 优先级 | 运算符           |
| :----- | :--------------- |
| 5      | * / % << >> & &^ |
| 4      | + - \| ^         |
| 3      | == != < <= > >=  |
| 2      | &&               |
| 1      | \|\|             |



### 2.4 循环结构

```go
for initialization;condition;post{
  //initialization 初始化值;
  //condition 判断条件;
  //post 本次循环后执行
}

for condition{
  //仅有判断条件的循环
  //传统的while循环
}

for{
  //传统的无限循环
}
```

-   循环结构均可以通过break或return语句终止

-   支持continue

-   支持goto

    -   ```go
        goto label;
        ..
        .
        label: statement;
        ```

    -   使用label作为标记点


### 2.5 选择结构

```go
switch var1 {
    case val1:
        ...
    case val2:
        ...
    default:
        ...
}
```

```go
select {
    case communication clause  :
       statement(s);      
    case communication clause  :
       statement(s); 
    /* 你可以定义任意数量的 case */
    default : /* 可选 */
       statement(s);
}
```

-   每个 case 都必须是一个通信

-   所有 channel 表达式都会被求值

-   所有被发送的表达式都会被求值

-   如果任意某个通信可以进行，它就执行，其他被忽略。

-   如果有多个 case 都可以运行，Select 会随机公平地选出一个执行。其他不会执行。

    否则：

    1.  如果有 default 子句，则执行该语句。
    2.  如果没有 default 子句，select 将阻塞，直到某个通信可以运行；Go 不会重新对 channel 或值进行求值。

### 2.6 注释

```go
// 单行注释
/*
 Author by 菜鸟教程
 我是多行注释
 */
```

### 2.7 标识符

-   一个或是多个字母(A~Z和a~z)数字(0~9)、下划线_组成的序列，但是第一个字符必须是字母或下划线而不能是数字

### 2.8 数据类型

-   布尔型
    -   `var b bool = true`
-   数字类型
    -   整型int和浮点型float32、float64，Go支持整形和浮点型，并且支持复数，其中位运算采用补码
-   字符串类型
    -   Go的字符串连接可以通过`+`实现
    -   Go的字符串是由单个字节连接起来的。
    -   Go字符串的字节使用UTF-8编码标识Unicode文本
-   派生类型
    -   指针类型（Pointer）
    -   数组类型
    -   结构化类型（struct）
    -   Channel类型
    -   函数类型
    -   切片类型
    -   接口类型
    -   Map类型
-   数字类型
    -   uint8 无符号8位整型（0-255）
    -   uint16 无符号16位整型（0-65535）
    -   uint32 无符号32位整型（0-2^32-1)
    -   uint64 无符号64位整型（0-2^64-1）
    -   int8 有符号8位整型（-128-127）
    -   int16 有符号16位整型（-2^8-2^8-1）
    -   int32 有符号32位整型（-2^16-2^16-1)
    -   int64 有符号64位整型（-2^32-2^32-1）
-   浮点类型
    -   float32   IEEE-754 32位浮点型数
    -   float64   IEEE-754 64位浮点型数
    -   complex64    32位实数和虚数
    -   complex128    64位实数和虚数
-   其他数字类型
    -   byte 类似于uint8
    -   rune 类似于int32
    -   uint 32或64位无符号整数
    -   int 32或64位有符号整数
    -   uintptr 无符号整型，用于存放一个指针

### 2.9 结构体

-   结构体可以储存同一类型的数据，但在结构体中可以定义不同的数据类型
-   结构体是由一系列具有相同类型或不同类型的集合
-   定义结构体

```go
type struct_variable_type struct {
   member definition
   member definition
   ...
   member definition
}
```

-   赋值

```go
variable_name := structure_variable_type {value1, value2...valuen}
或
variable_name := structure_variable_type { key1: value1, key2: value2..., keyn: valuen}
```

-   访问结构体成员

    -   `结构体.成员`

-   结构体指针

    -   Go语言可以定义指向结构体的指针类似于其他指针变量

    ```go
    var struct_pointer *Books
    ```

    -   指针变量可以存储结构体变量的地址。

    ```go
    struct_pointer = &Book1
    ```

    -   结构体指针访问结构体成员，使用"."

    ```go
    struct_pointer.title
    ```

    ```go
    type Books struct {
       title string
       author string
       subject string
       book_id int
    }
    
    func main() {
       var Book1 Books        /* 声明 Book1 为 Books 类型 */
       var Book2 Books        /* 声明 Book2 为 Books 类型 */
    
       /* book 1 描述 */
       Book1.title = "Go 语言"
       Book1.author = "www.runoob.com"
       Book1.subject = "Go 语言教程"
       Book1.book_id = 6495407
    
       /* book 2 描述 */
       Book2.title = "Python 教程"
       Book2.author = "www.runoob.com"
       Book2.subject = "Python 语言教程"
       Book2.book_id = 6495700
    
       /* 打印 Book1 信息 */
       printBook(&Book1)
    
       /* 打印 Book2 信息 */
       printBook(&Book2)
    }
    func printBook( book *Books ) {
       fmt.Printf( "Book title : %s\n", book.title)
       fmt.Printf( "Book author : %s\n", book.author)
       fmt.Printf( "Book subject : %s\n", book.subject)
       fmt.Printf( "Book book_id : %d\n", book.book_id)
    }
    ```

    以上实例执行运行结果为：

    ```console
    Book title : Go 语言
    Book author : www.runoob.com
    Book subject : Go 语言教程
    Book book_id : 6495407
    Book title : Python 教程
    Book author : www.runoob.com
    Book subject : Python 语言教程
    Book book_id : 6495700
    ```

    -   即，指针.member 可以直接获取值

### 2.10 defer

-   defer使用后，其定义的语句会在return语句执行之后执行，有压栈操作

-   

-   ```go
    func a() {
    i := 0
    defer fmt.Println(0) //因为i=0，所以此时就明确告诉golang在程序退出时，执行输出0的操作
    i++
    return
    }
    ```

    输出结果为0



### 2.11 切片

-   **切片和数组的异同**
    -   切片是指针类型，数组是值类型
    -   数组长度固定，切片可以看作为动态数组
    -   切片比数组多一个容量
    -   切片的底层是数组

-   切片是对数组的抽象
-   数组长度不变不适用某些场景
-   内置了类型切片("动态数组")
-   与数组想不长度不固定，可以追加元素，追加时可能使切片的容量增大

```go
var identifier []type
var slice1 []type = make([]type,len)
//简写
slic1 := make([]type,len)
//可以指定容量 capacity为可选参数，len为初始长度
make([]T,length,capacity)
```

-   切片初始化-直接初始化切片，[]为切片类型，{1,2,3}为内容，cap=len=3

`s := [] int{1,2,3}`

-   初始化s，是数组arr的引用

`s := arr[:]`

-   将arr中下标startIndex到endIndex-1下的元素创建一个新的切片

`s := arr[startIndex:]`

-   endIndex为最后一个元素

` s := arr[startIndex:endIndex]`

-   通过切片s初始化s1

`s1 := s[:]`

-   通过内置函数make初始化

`s := make([]int,len,cap)`

-   **len()函数**

`len(x)`表示当前数组/字符串长度

<img src="https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-19%2010.17.34.png" alt="截屏2021-11-19 10.17.34" style="zoom: 80%;" />

-   **cap()函数**

`cap(x)`表示当前数组长度

<img src="https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-19%2010.19.03.png" alt="截屏2021-11-19 10.19.03" style="zoom:80%;" />

-   **空(nil)切片**
    -   当一个切片在为初始化前默认为nil，长度为nil

-   切片截取

    -   可以通过设置上下限来设置截取切片

    ```go
    func main() {
       /* 创建切片 */
       numbers := []int{0,1,2,3,4,5,6,7,8}   
       printSlice(numbers)
    
       /* 打印原始切片 */
       fmt.Println("numbers ==", numbers)
    
       /* 打印子切片从索引1(包含) 到索引4(不包含)*/
       fmt.Println("numbers[1:4] ==", numbers[1:4])
    
       /* 默认下限为 0*/
       fmt.Println("numbers[:3] ==", numbers[:3])
    
       /* 默认上限为 len(s)*/
       fmt.Println("numbers[4:] ==", numbers[4:])
    
       numbers1 := make([]int,0,5)
       printSlice(numbers1)
    
       /* 打印子切片从索引  0(包含) 到索引 2(不包含) */
       number2 := numbers[:2]
       printSlice(number2)
    
       /* 打印子切片从索引 2(包含) 到索引 5(不包含) */
       number3 := numbers[2:5]
       printSlice(number3)
    
    }
    
    func printSlice(x []int){
       fmt.Printf("len=%d cap=%d slice=%v\n",len(x),cap(x),x)
    }
    ```

    执行以上代码输出结果为：

    ```console
    len=9 cap=9 slice=[0 1 2 3 4 5 6 7 8]
    numbers == [0 1 2 3 4 5 6 7 8]
    numbers[1:4] == [1 2 3]
    numbers[:3] == [0 1 2]
    numbers[4:] == [4 5 6 7 8]
    len=0 cap=5 slice=[]
    len=2 cap=9 slice=[0 1]
    len=3 cap=7 slice=[2 3 4]
    ```

-   append()函数

    -   追加元素

-   copy()函数

    -   复制内容

    ```go
    func main() {
       var numbers []int
       printSlice(numbers)
    
       /* 允许追加空切片 */
       numbers = append(numbers, 0)
       printSlice(numbers)
    
       /* 向切片添加一个元素 */
       numbers = append(numbers, 1)
       printSlice(numbers)
    
       /* 同时添加多个元素 */
       numbers = append(numbers, 2,3,4)
       printSlice(numbers)
    
       /* 创建切片 numbers1 是之前切片的两倍容量*/
       numbers1 := make([]int, len(numbers), (cap(numbers))*2)
    
       /* 拷贝 numbers 的内容到 numbers1 */
       copy(numbers1,numbers)
       printSlice(numbers1)   
    }
    
    func printSlice(x []int){
       fmt.Printf("len=%d cap=%d slice=%v\n",len(x),cap(x),x)
    }
    ```

### 2.12 范围（Range）

-   range关键字用于for循环中迭代数组(array)、切片(slice)、通道(channel)或集合(map)元素。在数组和切片中它返回元素的索引和索引对应的值，在集合中返回key-value键值对。
-   变相理解为传统的foreach
-   eg

```go
func main() {
    //这是我们使用range去求一个slice的和。使用数组跟这个很类似
    nums := []int{2, 3, 4}
    sum := 0
    for _, num := range nums {
        sum += num
    }
    fmt.Println("sum:", sum)
    //在数组上使用range将传入index和值两个变量。上面那个例子我们不需要使用该元素的序号，所以我们使用空白符"_"省略了。有时侯我们确实需要知道它的索引。
    for i, num := range nums {
        if num == 3 {
            fmt.Println("index:", i)
        }
    }
    //range也可以用在map的键值对上。
    kvs := map[string]string{"a": "apple", "b": "banana"}
    for k, v := range kvs {
        fmt.Printf("%s -> %s\n", k, v)
    }
    //range也可以用来枚举Unicode字符串。第一个参数是字符的索引，第二个是字符（Unicode的值）本身。
    for i, c := range "go" {
        fmt.Println(i, c)
    }
}
//以上实例运行输出结果为：
sum: 9
index: 1
a -> apple
b -> banana
0 103
1 111
```

### 2.13 类型转换

-   type(expression)

```go
func main() {
   var sum int = 17
   var count int = 5
   var mean float32
   
   mean = float32(sum)/float32(count)
   fmt.Printf("mean 的值为: %f\n",mean)
}
```

以上实例执行输出结果为：

```console
mean 的值为: 3.400000
```







## 三、函数

```go
func function_name( [parameter list] ) [return_types] {
   函数体
}
```

-   例

```go
/* 函数返回两个数的最大值 */
func max(num1, num2 int) int {
   /* 声明局部变量 */
   var result int

   if (num1 > num2) {
      result = num1
   } else {
      result = num2
   }
   return result 
}
```

### 3.1 函数调用

```go
func main() {
   /* 定义局部变量 */
   var a int = 100
   var b int = 200
   var ret int

   /* 调用函数并返回最大值 */
   ret = max(a, b)

   fmt.Printf( "最大值是 : %d\n", ret )
}
```

### 3.2 函数返回多值

-   Go函数可以返回多个值

-   ```go
    func swap(x, y string) (string, string) {
       return y, x
    }
    
    func main() {
       a, b := swap("Google", "Runoob")
       fmt.Println(a, b)
    }
    ```

### 3.3 函数参数

#### 3.3.1 值传递

-   传递是指在调用函数时将实际参数复制一份传递到函数中，这样在函数中如果对参数进行修改，将不会影响到实际参数。

    默认情况下，Go 语言使用的是值传递，即在调用过程中不会影响到实际参数。

```go
func main() {
   /* 定义局部变量 */
   var a int = 100
   var b int = 200

   fmt.Printf("交换前 a 的值为 : %d\n", a )
   fmt.Printf("交换前 b 的值为 : %d\n", b )

   /* 通过调用函数来交换值 */
   swap(a, b)

   fmt.Printf("交换后 a 的值 : %d\n", a )
   fmt.Printf("交换后 b 的值 : %d\n", b )
}

/* 定义相互交换值的函数 */
func swap(x, y int) int {
   var temp int

   temp = x /* 保存 x 的值 */
   x = y    /* 将 y 值赋给 x */
   y = temp /* 将 temp 值赋给 y*/

   return temp;
}
```

```console
交换前 a 的值为 : 100
交换前 b 的值为 : 200
交换后 a 的值 : 100
交换后 b 的值 : 200
```

-   即值传递不会改变调用函数的原值

#### 3.3.2 引用传递

```go
/* 定义交换值函数*/
func swap(x *int, y *int) {
   var temp int
   temp = *x    /* 保持 x 地址上的值 */
   *x = *y      /* 将 y 值赋给 x */
   *y = temp    /* 将 temp 值赋给 y */
}
/*调用*/
 swap(&a, &b)
```

-   即对地址直接操作，改变地址所指向的值，即更改了原值

### 3.4 函数用法

-   函数作为实参

    -   将变量视为函数

    -   ```go
        func main(){
           /* 声明函数变量 */
           getSquareRoot := func(x float64) float64 {
              return math.Sqrt(x)
           }
        
           /* 使用函数 */
           fmt.Println(getSquareRoot(9))
        
        }
        ```

-   闭包

    -   go支持匿名函数

    -   匿名函数是一个"内联"语句或表达式。

    -   **匿名函数的优越性在于可以直接使用函数内的变量，不必申明**

    -   ```go
        func getSequence() func() int {
           i:=0
           return func() int {
              i+=1
             return i  
           }	
        }
        
        func main(){
           /* nextNumber 为一个函数，函数 i 为 0 */
           nextNumber := getSequence()  
        
           /* 调用 nextNumber 函数，i 变量自增 1 并返回 */
           fmt.Println(nextNumber())
           fmt.Println(nextNumber())
           fmt.Println(nextNumber())
           
           /* 创建新的函数 nextNumber1，并查看结果 */
           nextNumber1 := getSequence()  
           fmt.Println(nextNumber1())
           fmt.Println(nextNumber1())
        }
        ```

    -   ```console
        1
        2
        3
        1
        2
        ```



## 四、Go方法

-   Go语言中同时有函数和方法。

-   一个方法就是一个包含了接受者的参数

-   接受者可以是命名类型或结构体类型的一个值或者是一个指针。

-   所有给定类型的方法属于该类型的方法集

-   ```go
    func (variable_name variable_data_type) function_name() [return_type]{
       /* 函数体*/
    }
    ```

-   ```go
    /* 定义结构体 */
    type Circle struct {
      radius float64
    }
    
    func main() {
      var c1 Circle
      c1.radius = 10.00
      fmt.Println("圆的面积 = ", c1.getArea())
    }
    
    //该 method 属于 Circle 类型对象中的方法
    func (c Circle) getArea() float64 {
      //c.radius 即为 Circle 类型对象中的属性
      return 3.14 * c.radius * c.radius
    }
    ```

-   





## 五、 Go指针

-   变量是一种方便的占位符，用于引用计算机内存地
-   通过`&a`可以获取变量a所在的内存地址

```go
var a = 100
fmt.Println(&a)
```

### 5.1 什么是指针

-   一个指针变量指向了一个值的内存地址
-   类似于变量，使用前也应声明

```go
var var_name *var-type
```

-   例

```go
var ip *int        /* 指向整型*/
var fp *float32    /* 指向浮点型 */
```

### 5.2 如何使用指针

-   定义指针
-   为指针变量赋值
-   访问指针变量中地址指向的值

-   若使用*则代表获取指针所指向的内容
-   此例子体现了修改内存地址指向的值可以直接改变变量的值

```go
var a = 100
var ap *int
ap = &a
*ap++
fmt.Println(*ap)
fmt.Println(a)
```

### 5.3 Go空指针

-   当一个指针只被定义而未被赋值时，它的值为nil

### 5.4 指针数组

-   `var ptr [Max]*int`

-   ```go
    const MAX int = 3
    
    func main() {
       a := []int{10,100,200}
       var i int
       var ptr [MAX]*int;
    
       for  i = 0; i < MAX; i++ {
          ptr[i] = &a[i] /* 整数地址赋值给指针数组 */
       }
    
       for  i = 0; i < MAX; i++ {
          fmt.Printf("a[%d] = %d\n", i,*ptr[i] )
       }
    }
    ```

### 5.5 指向指针的指针

```go
var ptr **int;
```

```go
func main() {

   var a int
   var ptr *int
   var pptr **int

   a = 3000

   /* 指针 ptr 地址 */
   ptr = &a

   /* 指向指针 ptr 地址 */
   pptr = &ptr

   /* 获取 pptr 的值 */
   fmt.Printf("变量 a = %d\n", a )
   fmt.Printf("指针变量 *ptr = %d\n", *ptr )
   fmt.Printf("指向指针的指针变量 **pptr = %d\n", **pptr)
}
```

### 5.6 函数或方法的指针参数

-   详见3.4引用传递



## 六、接口

```go
/* 定义接口 */
type interface_name interface {
   method_name1 [return_type]
   method_name2 [return_type]
   method_name3 [return_type]
   ...
   method_namen [return_type]
}

/* 定义结构体 */
type struct_name struct {
   /* variables */
}

/* 实现接口方法 */
func (struct_name_variable struct_name) method_name1() [return_type] {
   /* 方法实现 */
}
...
func (struct_name_variable struct_name) method_namen() [return_type] {
   /* 方法实现*/
}
```

-   eg

```go
type Phone interface {
    call()
}

type NokiaPhone struct {
}

func (nokiaPhone NokiaPhone) call() {
    fmt.Println("I am Nokia, I can call you!")
}

type IPhone struct {
}

func (iPhone IPhone) call() {
    fmt.Println("I am iPhone, I can call you!")
}	
```



## 七、错误处理

-   Go通过内部的错误接口提供了分寸简单的错误处理机制

```go
type error interface {
    Error() string
}
```

-   可以实现此接口生产错误信息

```go
func Sqrt(f float64) (float64, error) {
    if f < 0 {
        return 0, errors.New("math: square root of negative number")
    }
    // 实现
  	result, err:= Sqrt(-1)

		if err != nil {
   			fmt.Println(err)
		}
}
```

-   eg

```go
// 定义一个 DivideError 结构
type DivideError struct {
    dividee int
    divider int
}

// 实现 `error` 接口
func (de *DivideError) Error() string {
    strFormat := `
    Cannot proceed, the divider is zero.
    dividee: %d
    divider: 0
`
    return fmt.Sprintf(strFormat, de.dividee)
}

// 定义 `int` 类型除法运算的函数
func Divide(varDividee int, varDivider int) (result int, errorMsg string) {
    if varDivider == 0 {
            dData := DivideError{
                    dividee: varDividee,
                    divider: varDivider,
            }
            errorMsg = dData.Error()
            return
    } else {
            return varDividee / varDivider, ""
    }

}

func main() {

    // 正常情况
    if result, errorMsg := Divide(100, 10); errorMsg == "" {
            fmt.Println("100/10 = ", result)
    }
    // 当除数为零的时候会返回错误信息
    if _, errorMsg := Divide(100, 0); errorMsg != "" {
            fmt.Println("errorMsg is: ", errorMsg)
    }

}
```

-   执行以上程序，输出结果为：

```
100/10 =  10
errorMsg is:  
    Cannot proceed, the divider is zero.
    dividee: 100
    divider: 0
```





## 八、并发

### 8.1 并发

-   go语言支持并发，使用关键字`go`开启
-   goroutine是轻量级线程，goroutine的调度是有Golang运行时进行管理的

`go 函数名(参数列表)`

-   eg

```go
func say(s string)  {
	for i := 0;i<5;i++ {
		time.Sleep(100+time.Millisecond)
		fmt.Println(s)
	}
}

func main() {
	go say("hello")
	say("world")
}
```

### 8.2 通道 channel

-   通道时用来传递数据的一个数据结构

-   通道可以用于两个goroutine之间通过一个指定类型来同步运行和通讯。

-   <-用于指明通道的方向，发送或者接受。

-   如果未指定则代表双向通道

    ```go
    ch <-v  //把v发送到通道ch
    
    v:= <-ch //从ch接收数据并赋值给v
    ```

-   声明通道

`ch := make(chan int)`

-   eg

```go
func sum(s []int, c chan int) {
        sum := 0
        for _, v := range s {
                sum += v
        }
        c <- sum // 把 sum 发送到通道 c
}

func main() {
        s := []int{7, 2, 8, -9, 4, 0}

        c := make(chan int)
        go sum(s[:len(s)/2], c)
        go sum(s[len(s)/2:], c)
        x, y := <-c, <-c // 从通道 c 中接收

        fmt.Println(x, y, x+y)
}
```

### 8.3 通道缓冲区

-   make的第二个参数设置缓冲区大小

`ch := make(chan int,100)`

-   带缓冲区的通道允许发送端的数据发动和接收端端数据获取处于异步状态，也就是说发送端的数据可以存在缓冲区里，可以等待接收端去获取数据，而不必立刻需要接收端去获取数据
-   由于缓冲区大小有限，因此还是不需要有接收端来接收数据，否则缓冲区一满，数据端就无法再发送数据了
-   注：如果通道不带缓存，发送方会阻塞直到接收方从通道中接收了值。如果带缓冲，发送方则会阻塞直到发送的值被拷贝到缓冲区，如果缓冲区满则依旧会阻塞，直到某个接收方接收到值

### 8.4 遍历通道与关闭通道

-   可以通过range实现遍历，类似于数组或切片

`v,ok := <-ch`

-   通过close()关闭通道

-   eg

```go
func fibonacci(n int, c chan int) {
        x, y := 0, 1
        for i := 0; i < n; i++ {
                c <- x
                x, y = y, x+y
        }
        close(c)
}

func main() {
        c := make(chan int, 10)
        go fibonacci(cap(c), c)
        // range 函数遍历每个从通道接收到的数据，因为 c 在发送完 10 个
        // 数据之后就关闭了通道，所以这里我们 range 函数在接收到 10 个数据
        // 之后就结束了。如果上面的 c 通道不关闭，那么 range 函数就不
        // 会结束，从而在接收第 11 个数据的时候就阻塞了。
        for i := range c {
                fmt.Println(i)
        }
}
```









## 六、包

###  6.1 os

-   os 动态数组

```go
//写法等价
fmt.Println(strings.Join(os.Args[1:]," "))
fmt.Println(os.Args[1:])
```

### 6.2 fmt

-   fmt.printf()

![791637203215_.pic](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/791637203215_.pic.jpg)

## 七、API

### 7.1 map

```go
/* 声明变量，默认 map 是 nil */
var map_variable map[key_data_type]value_data_type

/* 使用 make 函数 */
map_variable := make(map[key_data_type]value_data_type)
```

-   eg

```go
func main() {
    var countryCapitalMap map[string]string /*创建集合 */
    countryCapitalMap = make(map[string]string)

    /* map插入key - value对,各个国家对应的首都 */
    countryCapitalMap [ "France" ] = "巴黎"
    countryCapitalMap [ "Italy" ] = "罗马"
    countryCapitalMap [ "Japan" ] = "东京"
    countryCapitalMap [ "India " ] = "新德里"

    /*使用键输出地图值 */ 
    for country := range countryCapitalMap {
        fmt.Println(country, "首都是", countryCapitalMap [country])
    }

    /*查看元素在集合中是否存在 */
    capital, ok := countryCapitalMap [ "American" ] /*如果确定是真实的,则存在,否则不存在 */
    /*fmt.Println(capital) */
    /*fmt.Println(ok) */
    if (ok) {
        fmt.Println("American 的首都是", capital)
    } else {
        fmt.Println("American 的首都不存在")
    }
}
```

-   创建值 map[key] = value

-   获取值 map[key]

    -   若无此key-value，则返回0或nil

-   delete()函数

    -   ``` go
        func main() {
                /* 创建map */
                countryCapitalMap := map[string]string{"France": "Paris", "Italy": "Rome", "Japan": "Tokyo", "India": "New delhi"}
        
                fmt.Println("原始地图")
        
                /* 打印地图 */
                for country := range countryCapitalMap {
                        fmt.Println(country, "首都是", countryCapitalMap [ country ])
                }
        
                /*删除元素*/ delete(countryCapitalMap, "France")
                fmt.Println("法国条目被删除")
        
                fmt.Println("删除元素后地图")
        
                /*打印地图*/
                for country := range countryCapitalMap {
                        fmt.Println(country, "首都是", countryCapitalMap [ country ])
                }
        }
        ```

    -   delete(map,key)

-   Go实现HashMap

```go
import (
    "fmt"
)

type HashMap struct {
    key string
    value string
    hashCode int
    next *HashMap
}

var table [16](*HashMap)

func initTable() {
    for i := range table{
        table[i] = &HashMap{"","",i,nil}
    }
}

func getInstance() [16](*HashMap){
    if(table[0] == nil){
        initTable()
    }
    return table
}

func genHashCode(k string) int{
    if len(k) == 0{
        return 0
    }
    var hashCode int = 0
    var lastIndex int = len(k) - 1
    for i := range k {
        if i == lastIndex {
            hashCode += int(k[i])
            break
        }
        hashCode += (hashCode + int(k[i]))*31
    }
    return hashCode
}

func indexTable(hashCode int) int{
    return hashCode%16
}

func indexNode(hashCode int) int {
    return hashCode>>4
}

func put(k string, v string) string {
    var hashCode = genHashCode(k)
    var thisNode = HashMap{k,v,hashCode,nil}

    var tableIndex = indexTable(hashCode)
    var nodeIndex = indexNode(hashCode)

    var headPtr [16](*HashMap) = getInstance()
    var headNode = headPtr[tableIndex]

    if (*headNode).key == "" {
        *headNode = thisNode
        return ""
    }

    var lastNode *HashMap = headNode
    var nextNode *HashMap = (*headNode).next

    for nextNode != nil && (indexNode((*nextNode).hashCode) < nodeIndex){
        lastNode = nextNode
        nextNode = (*nextNode).next
    }
    if (*lastNode).hashCode == thisNode.hashCode {
        var oldValue string = lastNode.value
        lastNode.value = thisNode.value
        return oldValue
    }
    if lastNode.hashCode < thisNode.hashCode {
        lastNode.next = &thisNode
    }
    if nextNode != nil {
        thisNode.next = nextNode
    }
    return ""
}

func get(k string) string {
    var hashCode = genHashCode(k)
    var tableIndex = indexTable(hashCode)

    var headPtr [16](*HashMap) = getInstance()
    var node *HashMap = headPtr[tableIndex]

    if (*node).key == k{
        return (*node).value
    }

    for (*node).next != nil {
        if k == (*node).key {
            return (*node).value
        }
        node = (*node).next
    }
    return ""
}

```

```go
//examples 
func main() {
    getInstance()
    put("a","a_put")
    put("b","b_put")
    fmt.Println(get("a"))
    fmt.Println(get("b"))
    put("p","p_put")
    fmt.Println(get("p"))
}
```
