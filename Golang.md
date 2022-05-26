# Golang

## 资料来源

> [Go语言学习之路/Go语言教程 | 李文周的博客 (liwenzhou.com)](https://www.liwenzhou.com/posts/Go/golang-menu/)

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



