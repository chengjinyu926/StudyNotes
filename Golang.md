# Golang

## 资料来源

> [Go语言学习之路/Go语言教程 | 李文周的博客 (liwenzhou.com)](https://www.liwenzhou.com/posts/Go/golang-menu/)
>
> [前景 · Go语言中文文档 (topgoer.com)](https://www.topgoer.com/)

## 切片

​	存储相同类型元素的可变长序列。。切片是引用类型，它的内部结构包含***地址***、***长度***、***容量***。其基于数组做了一层封装。

### 获得切片的方式

1. 直接定义

```go
var a []string//声明一个string类型的切片，其值为nil
a := []int{}//声明一个int类型的切片并给切片赋值，其值不为nil
b := []int{2: 3, 5} //[0 0 3 5]
c := []int{1, 3, 5} //[1,3,5]
```

2. 基于数组或切片定义

```go
a := [5]int{1, 2, 3, 4, 5}//a可以是数组或切片
b := a[1:3]//从数组下标为1的元素取到下标为3的元素（切片不包含此元素）截止
c := a[:3]//取数组a内元素 1 2 3
d := a[1:]//取数组a内元素 2 3 4 5
e := a[:]//取数组内全部元素
f := a[2:4:5]//[3,4] 长度为2(4-2) 容量为3(5-2)
```

3. 使用make函数

```go
a := make([]int, 5)//[0 0 0 0 0] 长度5 容量5
a := make([]int, 5, 10)//[0 0 0 0 0] 长度5 容量10
```

### 给切片添加元素

​	使用append()得到一个拼接元素后的切片

```go
var a []int//[]
a = append(a, 1)//[1] 添加单个元素
a = append(a, 2, 3)//[1 2 3] 添加多个元素
a = append(a, a...)//[1 2 3 1 2 3] 添加某个切片中的元素
```

### 扩容机制

```go
var numSlice []int
	for i := 0; i < 10; i++ {
		numSlice = append(numSlice, i)
		fmt.Printf("%v  len:%d  cap:%d  ptr:%p\n", numSlice, len(numSlice), cap(numSlice), numSlice)
	}
/* 输出结果
[0]  len:1  cap:1  ptr:0xc0000aa058
[0 1]  len:2  cap:2  ptr:0xc0000aa0a0
[0 1 2]  len:3  cap:4  ptr:0xc0000a8080
[0 1 2 3]  len:4  cap:4  ptr:0xc0000a8080
[0 1 2 3 4]  len:5  cap:8  ptr:0xc0000c40c0
[0 1 2 3 4 5]  len:6  cap:8  ptr:0xc0000c40c0
[0 1 2 3 4 5 6]  len:7  cap:8  ptr:0xc0000c40c0
[0 1 2 3 4 5 6 7]  len:8  cap:8  ptr:0xc0000c40c0
[0 1 2 3 4 5 6 7 8]  len:9  cap:16  ptr:0xc0000d4080
[0 1 2 3 4 5 6 7 8 9]  len:10  cap:16  ptr:0xc0000d4080
*/
var numSlice []int
var capacity = cap(numSlice)
for i := 0; i < 10000; i++ {
	if capacity != cap(numSlice) {
		capacity = cap(numSlice)
		print(" ", capacity)
	}
	numSlice = append(numSlice, i)
}
/* 输出结果
 1 2 4 8 16 32 64 128 256 512 848 1280 1792 2560 3408 5120 7168 9216 12288
*/
```

#### 扩容机制源代码

```go
newcap := old.cap
doublecap := newcap + newcap
if cap > doublecap {
	newcap = cap
} else {
	const threshold = 256
	if old.cap < threshold {
		newcap = doublecap
	} else {
		// Check 0 < newcap to detect overflow
		// and prevent an infinite loop.
		for 0 < newcap && newcap < cap {
			// Transition from growing 2x for small slices
			// to growing 1.25x for large slices. This formula
			// gives a smooth-ish transition between the two.
			newcap += (newcap + 3*threshold) / 4
		}
		// Set newcap to the requested cap when
		// the newcap calculation overflowed.
		if newcap <= 0 {
			newcap = cap
		}
	}
}
```

* 1.18版本
  * 首先判断，如果新申请容量（cap）大于2倍的旧容量（old.cap），最终容量（newcap）就是新申请的容量（cap）。
  * 否则判断，如果旧切片的长度小于门槛（256），则最终容量(newcap)就是旧容量(old.cap)的两倍，即（newcap=doublecap），
  * 否则判断，如果旧切片长度大于等于1024，则最终容量（newcap）从旧容量（old.cap）开始循环增加，直到最终容量（newcap）大于等于新申请的容量(cap)
  * 如果最终容量（newcap）计算值异常，则最终容量（cap）就是新申请容量（cap）。
* 历史版本
  * 首先判断，如果新申请容量（cap）大于2倍的旧容量（old.cap），最终容量（newcap）就是新申请的容量（cap）。
  * 否则判断，如果旧切片的长度小于1024，则最终容量(newcap)就是旧容量(old.cap)的两倍，即（newcap=doublecap），
  * 否则判断，如果旧切片长度大于等于1024，则最终容量（newcap）从旧容量（old.cap）开始循环增加原来的1/4，即（newcap=old.cap,for {newcap += newcap/4}）直到最终容量（newcap）大于等于新申请的容量(cap)
  * 如果最终容量（newcap）计算值异常，则最终容量（newcap）就是新申请容量（cap）。

### 切片间赋值

​	切片间直接赋值是将地址赋值，可以利用copy()完成值复制。

```go
	a := []int{1, 2, 3, 4, 5}
	b := a
	c := make([]int, 4)
	copy(c, a)
	b[1] = 0
	fmt.Printf("%v\n", a)//[1 0 3 4 5]
	fmt.Printf("%v\n", b)//[1 0 3 4 5]
	fmt.Printf("%v\n", c)//[1 2 3 4 5]
```

### 切片删除元素

​	利用append()删除切片间元素

```go
	a := []int{1, 2, 3, 4, 5}
	a = append(a[:2], a[3:]...)
```



## MAP

​	提供映射关系的容器，其底层使用散列表（Hash）实现。

​	无序的基于 ***key-value*** 的数据结构。是引用类型，必须初始化才可以使用。

### 使用

````go
var a map[string]int = make(map[string]int, 8)//声明并利用make函数分配内存 8是容量
b := map[string]int{//声明时填充元素
		"程金玉": 22,
		"杜鑫宇": 22,
}
value, ok := b["key"]//判断键是否在，存在则value值为键对应的值，ok 是布尔型
delete(b, "key")//删除键值对
````

## 函数

​	Go语言中使用 ***func*** 定义函数。

​	函数是一种数据类型。它可以作为参数，也可以作为返回值。

```go
func 函数名 (参数) (返回值){
    函数体
}
```

* 函数名：由字母、数字、下划线组成，不能以数字开头。同一个包内，函数名不可以重名。
* 参数：参数由参数变量和参数变量的类型组成。

* 返回值：有返回值变量和返回值变量类型组成，也可以只写返回值的类型。

### 函数类型与变量

#### calculation 函数类型

​	接受两个 ***int*** 类型的参数并返回一个 ***int*** 类型的返回值。

### 将函数赋值给变量

```go
func add(x, y int) int {
	println(x + y)
	return x + y
}

func sub(x, y int) int {
	println(x - y)
	return x - y
}

func main() {
	a := add
	b := sub
	a(1, 2)
	b(3, 2)
}
```

### 匿名函数

匿名函数没有函数名，需要保存到某个变量或立刻执行

```go
func main() {
    //保存到某个变量
	caculate := func(x, y int) (r1, r2 int) {
		return x + y, x - y
	}
	r1, r2 := caculate(15, 20)\
}
```

立刻执行匿名函数

```go
	r := func(x, y string) string {
		return x + y
	}("程", "金玉")
```

### 函数作为参数

​	函数可以作为参数使用。

```go
func add(x, y int) int {
	return x + y
}

func sub(x, y int) int {
	return x - y
}

func caclulate(x, y int, op func(int, int) int) int {
	return op(x, y)
}

func main() {
	println(caclulate(15, 20, add))
	println(caclulate(15, 10, sub))
}
```

### 函数作为返回值

​	函数可以作为返回值

```go
func add(x, y int) int {
	return x + y
}

func sub(x, y int) int {
	return x - y
}

func caclulate(x int) func(int, int) int {
	if x == 1 {
		return sub
	} else if x == 2 {
		return add
	} else {
		return add
	}
}

func main() {
	fmt.Println(caclulate(1)(15, 20))
}
```

### 闭包

​	闭包 = 函数 + 引用环境 。

```go
func add() func(int) int {
	v := 0
	return func(y int) int {
		v += y
		return v
	}
}

func main() {
	f := add()
	println(f(1))
	println(f(2))
}
```

```go
func add(v int) func(int) int {
	return func(y int) int {
		v += y
		return v
	}
}

func main() {
	f := add(10)
	println(f(1))
	println(f(2))
}
```

```go
func foodFactory(mode string) func(string) string {
	return func(name string) string {
		if !strings.HasSuffix(name, mode) {
			return name + mode
		}
		return name
	}
}

func main() {
	p1 := foodFactory("罐头")
	p2 := foodFactory("蛋糕")
	println(p1("草莓"))
	println(p2("草莓"))
	println(p1("苹果"))
	println(p2("苹果"))
}
```

```go
func cal(inital int) (x func(int) int, y func(int) int) {
	addInit := inital
	subInit := inital
	add := func(input int) int {
		addInit += input
		return addInit
	}
	sub := func(input int) int {
		subInit -= input
		return subInit
	}
	return add, sub
}
func main() {
	a, s := cal(50)
	println(a(1), s(1))
	println(a(13), s(2))
	println(a(66), s(10))
}
```

### defer

​	将跟在其后的语句作延迟处理。在 ***defer*** 归属的函数即将返回时，被 ***defer*** 修饰的语句将按照逆序顺序依次执行。

​	由于 ***defer*** 延迟执行的特性，所以 ***defer*** 关键字可以非常方便的处理资源释放等问题。

 	***defer*** 注册的语句中要执行的所有参数都需要确定其值。

#### 执行时机

​	Go 语言中，***return*** 在底层并不是原子操作。它分为给返回值复制和 ***RET*** 指令两步，而 ***defer*** 语句执行的时机处于给返回值赋值之后，***RET*** 指令之前。

> 原子性：要么完成执行，要么完全不执行

### panic 和 recover

​	panic：引发一个错误。

​	recover: 处理错误，使程序继续执行下去。必须配合 ***defer*** 使用

```go
func a() {
	println("A")
}
func b() {
	println("B")
	defer func() {
		err := recover()
		if err != nil {
			println("Recover")
		}
	}()
	panic("error")
}
func c() {
	println("C")
}
func main() {
	a()
	b()
	c()
}
```

## 指针

​	区别于C/C++的指针，GO语言中的指针不能进行偏移和运算，是安全指针。

​	任何程序数据载入内存后，在内存中都有属于自己的地址，这就是指针。而为了保存一个数据在内存中的地址，我们需要指针变量。

### &

​	取地址操作符，取变量的地址值。

### *

​	根据地址取到地址指向的值。

## 结构体

​	Go语言中没有类的概念，也不支持类的继承等面向对象的概念。Go语言中通过结构提的内嵌再配合接口比面向对象具有更高的扩展性和灵活性。

### 自定义类型

​	Go语言中使用 ***type*** 关键字来定义自定义类型。

​	自定义类型定义了一个全新的类型。我们可以基于内置的基本类型定义，也可以通过 ***struct*** 定义。

```go
type MyInt int//MyInt 是一种新的类型，它具有int的特性。
```

### 匿名结构体

```go
func main() {
	var cat struct {
		name string
		age  int
	}
	cat.age = 18
	cat.name = "tom"
	fmt.Printf("%v\n", cat)
}
```

### 类型别名

​	TypeAlias 只是 Type 的别名，本质上TypeAlias与Type是同一个类型。

```go
type byte = uint8
type rune = int32
```

### 定义结构体

​	使用 ***struct*** 和 ***type*** 定义结构体。

```go
type cat struct {
	age      int
	weight   float32
	likeFood []string
}

type mouse struct {
	age      int
	weight   float32
	likeFood []string
}

func main() {
	var tom cat
	tom.age = 18
	tom.weight = 25.9
	tom.likeFood = []string{"jerry", "fish"}

	jerry := mouse{
		age:      11,
		weight:   0.15,
		likeFood: []string{"cheess", "bread"},
	}

	ketty := cat{
		17,
		18.6,
		[]string{"fruit juice", "banana"},
	}
	fmt.Printf("%v\n", tom)
	fmt.Printf("%#v\n", jerry)
	fmt.Printf("%v\n", ketty)
}
```



### 内存布局

​	结构体占用一块连续的内存。

​	空结构体不占用内存空间。

### 构造函数

​	Go语言的结构体没有构造函数。

​	***struct*** 是值类型，如果结构体比较复杂，值拷贝性能开销会比较大，所以构造体函数返回的是结构体指针类型。

```go
type cat struct {
	name string
	age  int
}

func newCat(name string, age int) *cat {
	return &cat{
		name,
		age,
	}
}
```

### 方法和接收者

​	方法（ ***method*** ）是一种作用于特定类型的变量的函数。这种特定类型变量叫做接收者（ ***receiver*** ）。接收者的概念就类似于其它语言中的 ***this*** 或 ***self*** 。

```go
type cat struct {
	name string
	age  int
}

func newCat(name string, age int) *cat {
	return &cat{
		name,
		age,
	}
}

func (c cat) Dream(mouseName string) bool {
	fmt.Printf("%v在梦中想要抓到%v\n", c.name, mouseName)
	return false
}

func main() {
	tom := newCat("tom", 19)
	fmt.Print(tom.Dream("jerry"))
}
```

#### 指针类型的接收者

​	指针类型的接收者由一个结构体指针构成，由于指针的特性，调用方法时修改接收者指针的任意成员变量，在方法结束后，修改都是有效的。

```go
type cat struct {
	name string
	age  int
}

func newCat(name string, age int) *cat {
	return &cat{
		name,
		age,
	}
}

func (c *cat) grow() {
	c.age++
}

func main() {
	tom := newCat("tom", 19)
	tom.grow()
	fmt.Print(tom.age)
}
```

##### 应用场景

1. 需要修改修改接收者中的值
2. 接收者是拷贝代价比较大的对象
3. 保证一致性，如果有某个方法使用了指针接收者，那么其他方法也应该使用指针接收者。

### 任意类型添加方法

​	在Go语言中，接收者的类型可以是任何类型，不仅仅是结构体，任何类型都可以拥有方法。 

#### 注意事项

1. 非本地类型不能定义方法，即不可以给别的包的类型定义方法。

### 结构体的字段

​	结构体中字段大写开头表示可公开访问，小写表示私有（仅在定义当前结构体的包中可访问）。

#### 结构体的匿名字段

​	结构体允许其成员字段在声明的时候只有类型而名称，这种没有名字的字段即匿名字段。

​	匿名字段并非没有字段名，其字段名默认采用类型名。结构体中的字段名必须唯一，所以结构体中同种类型的匿名字段只能有一个。

```go
func main() {
	var cat struct {
		string
		int
	}
	cat.int = 18
	cat.string = "tom"
	fmt.Printf("%v\n", cat)
}
```

### 嵌套结构体

​	一个结构体中可以嵌套包含另一个结构体或指针。

```go
type user struct {
	id      int
	address Address
}

type Address struct {
	id    int
	value string
}

func main() {
	mark := user{
		15,
		Address{
			1,
			"曹县",
		},
	}
	fmt.Printf("%v", mark)
}
```

#### 注意事项

1. 嵌套的结构体也可以采用匿名字段的方式，访问匿名结构体的内部字段时，可以省略匿名结构体的名称。当访问多个匿名结构体相同的名称相同的字段时，则无法再省略匿名结构体的名称。

### 结构体的”继承“

​	可以通过嵌套结构体实现继承。

```go
type animal struct {
	name string
}

type dog struct {
	*animal
}

type haski struct {
	*dog
}

func (a animal) move() {
	fmt.Println(a.name, "移动了")
}

func (d dog) wang() {
	fmt.Println("汪汪汪！")
}

func (h haski) broken() {
	fmt.Println("哈士奇拆家了")
}

func main() {
	mike := haski{
		&dog{
			&animal{
				"mike",
			},
		},
	}
	mike.move()
	mike.wang()
	mike.broken()
}
```

### 结构体与JSON

```go
//创造学生结构体和班级结构体
//将班级结构体的json打印至控制台
//将json转为结构体

type Student struct {
	ID   int
	Name string
}

type Class struct {
	Name     string
	Students []*Student
}

func (c *Class) addStudent2Class(name string, id int) {
	c.Students = append(c.Students, &Student{
		ID:   id,
		Name: name,
	})
}

func main() {
	class := Class{
		"计科六班",
		[]*Student{},
	}
	class.addStudent2Class("程金玉", 1)
	class.addStudent2Class("江睿", 2)
	class.addStudent2Class("杨芳荣", 3)
	class.addStudent2Class("于乐乐", 4)
	class.addStudent2Class("郑园园", 5)
	r, err := json.Marshal(class)
	if err != nil {
		return
	}
	fmt.Printf("%s\n", r)
	cpClas := &class
	err2 := json.Unmarshal([]byte(r), cpClas)
	if err2 != nil {
		return
	}
	fmt.Printf("%v\n", cpClas)
}
```

### 结构体标签

​	***Tag*** 是结构体的元信息，可以在运行的时候通过反射读取。

​	***Tag*** 在结构体的后方定义，由一对反引号包裹。

​	结构体 ***Tag*** 由一个或多个键值对组成。键与值使用冒号分隔，值用双引号括起来。同一个结构体字段可以设置多个 ***Tag*** ，不同的键值对之间使用空格分隔。

​	为结构体编写 ***Tag*** 时，必须严格遵守键值对的规则。一旦格式写错。编译时和运行时都不会报错，通过反射也无法正确取值。

```go
type Student struct {
	ID     int
	Name   string
	Gender string `json:"sex"`
}

func main() {
	p1 := Student{
		1,
		"lily",
		"女",
	}
	data, _ := json.Marshal(p1)
	fmt.Printf("%s", data)
}
```

## 包

​	在工程化的Go语言开发项目中，Go语言的源码复用是建立在包（ ***package*** ）基础之上的。

​	一个包是由一个或多个Go源码文件（.go结尾的文件）组成，是一种高级的代码复用方案，Go语言为我们提供了很多内置包，如`fmt`、`os`、`io`等。

一个包可以简单理解为一个存放`.go`文件的文件夹。

### 定义包

​	使用 `package` 关键字定义包，包名不可以包含 `_` 符号。

```go
package main

func main() {

}
```

​	一个文件夹下的直接包含的文件只能归属一个包，同一个包的文件不能在多个文件夹下。

​	包名为`main`的包是应用程序的入口包，这种包编译后会得到一个可执行文件。

### 标识符可见性

​	如果想让一个包中的标识符（变量、常量、类型、函数等）能被外部的包所用，那么标识符必须对外是可见的。

​	通过标识符的的首字母大小写来控制标识符的对外可见（首字母大写）或不可见。

### 包的引入

​	要在当前包中使用另外一个包的内容需要使用`import`关键字。

​	Go语言中禁止循环导包。

```go
import (
	_ "encoding/json"//导入包但不使用 可以使用 匿名标识符
	fm "fmt"
)

func main() {
	fm.Printf("SS")
}
```

### Init初始化函数

​	在每一个Go源文件中，都能定义任意个初始化函数。

​	程序启动时，初始化函数会按照它们声明的顺序执行。

​	初始化函数不接受任何参数，也没有任何返回值，也不能在代码中主动调用。

​	初始化函数执行的顺序和包导入的顺序一致

```go
func init() {
	println("init1...")
}

func init() {
	println("init2...")
}

func main() {
}
```

## Go module

​	Go module 是 Go1.11 版本发布的依赖管理方案，从 Go1.14 版本开始推荐在生产环境使用，于Go1.16版本默认开启。

### 相关命令

|      命令       |                   介绍                   |
| :-------------: | :--------------------------------------: |
|   go mod init   |  初始化项目依赖，生成 ***go.mod*** 文件  |
| go mod download |         根据 go.mod 文件下载依赖         |
|   go mod tidy   | 将项目文件中引入的依赖与 go.mod 进行比对 |
|  go mod graph   |              输出依赖关系图              |
|   go mod edit   |             编辑 go.mod 文件             |
|  go mod vendor  | 将项目所有的依赖导出至 ***vendor*** 目录 |
|  go mod verify  |        检查一个依赖包是否被篡改过        |
|   go mod  why   |          解释为什么需要某个依赖          |

### GOPROXY

​	环境变量，主要设置Go模块代理（Go module proxy）,其作用是用于使Go在后续拉取模块版本时能够脱离传统的VCS方式，直接通过镜像站点来快速拉取。

​	GOPROXY的默认值是`https://proxy.golang.org,direct`。国内可能无法正常访问该地址，所以我们通常需要配置一个可访问的地址。

* https://goproxy.cn
* https://goproxy.io

​	配置GOPROXY命令如下：

```powershell
go env -w GOPROXY=https://goproxy.cn,direct
```

​	GOPROXY允许设置多个代理地址，多个地址之间需要用 `,` 分割。最后的`direct`用于指示Go回源到源地址去抓取，即回到源地址去抓取。

​	当配置多个代理地址时，如果第一个代理地址返回404或410错误时，Go会自动尝试下一个代理地址。

### GOPRIVATE

​	
