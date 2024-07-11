# Go数组和切片
## 概述
- Go 语言的切片类型属于**引用类型**，同属引用类型的还有字典类型、通道类型、函数类型等
- Go 语言的数组类型则属于**值类型**，同属值类型的有基础数据类型以及结构体类型。
- Go 语言里不存在像 Java 等编程语言中令人困惑的“传值或传引用”问题。在 Go 语言中，我们判断所谓的“传值”或者“传引用”只要看被传递的值的类型就好了。还有注意的是&值类型也是传引用。
## 数组
Go 语言中的数组是一种 值类型（即其声明的变量存储的是值，不像 C/C++ 中是指向首元素的指针），所以可以通过 new() 来创建： var arr1 = new([5]int)。
如何理解数组是值类型呢？可以通过下面的例子理解：
``` go
func main() {
    array := [3]int{1, 2, 3}
    // 数组传递到函数中
    test(array)
    fmt.Printf("array 外: %p\n", &amp;array)
}
func test(array [3]int) {
    fmt.Printf("array 内: %p\n", &amp;array)
}
```
可以发现地址不同，说明是值传递，函数内又新创建了一个副本。
```go
func main() {
    array1 := [3]int{1, 2, 3}
    var array2 [3]int
    array2 = array1
    // 修改array1的值
    array1[0] = 10
    fmt.Println(array1)
    fmt.Println(array2)
  }  
```
可以发现array2的值并没有改变，这也说明是值传递，创建了新的数组副本。
> 注意：数组长度也是数组类型的一部分，所以 [5] int 和 [10] int 是属于不同类型的

如果你想修改原数组，那么 arr2 必须通过 & 操作符以引用方式传过来，例如 func1 (&arr2）
```go
package main
import "fmt"
func f(a [3]int) { fmt.Println(a) }
func fp(a *[3]int) { fmt.Println(a) }

func main() {
    var ar [3]int
    f(ar)     // passes a copy of ar
    fp(&ar)   // passes a pointer to ar
}

输出：
[0 0 0]
&[0 0 0]
```

## 切片
1. 有了数组为什么还需要切片呢？
- 切片可以帮我们动态扩容，实现动态数组的效果。
- 切片相当于是对数组的封装，因为它的底层就是数组，所以屏蔽了扩容的细节。
- 面对一开始难以定义合适大小数组的场景很有用，比如从文件或者网络中读取数据等场景。
    ```go
    type slice struct{
        array unsafe.Pointer
        len int
        cap int
    }
    ```
2. 切片（slice）是对数组一个连续片段的引用（该数组我们称之为相关数组，通常是匿名的），所以**切片是一个引用类型**（因此更类似于 C/C++ 中的数组类型）
3. 切片声明
    ```go
    声明切片的格式是： var identifier []type（不需要说明长度）。
    一个切片在未初始化之前默认为 nil，长度为 0。
    切片的初始化格式是：var slice1 []type = arr1[start:end]。
    ```
4. 如果你有一个函数需要对数组做操作，你可能总是需要把参数声明为切片。当你调用该函数时，把数组分片，创建为一个切片引用并传递给该函数。
    ```go
    func sum(a []int) int {
        s := 0
        for i := 0; i < len(a); i++ {
            s += a[i]
        }
        return s
    }

    func main() {
        var arr = [5]int{0, 1, 2, 3, 4}
        sum(arr[:])
    }
    ```
5. 当相关数组还没有定义时，我们可以使用 make () 函数来创建一个切片 同时创建好相关数组：```var slice1 []type = make([]type, len)```
6. **如果你只想修改切片中元素的值，而不会更改切片的容量与指向，则可以按值传递切片，否则你应该考虑按指针传递。**

因为如果指向底层数组的指针被覆盖或者修改（copy、重分配、**append触发扩容**），此时函数内部对数据的修改将不再影响到外部的切片，代表长度的len和容量cap也均不会被修改。
![](https://pic.imgdb.cn/item/668fe325d9c307b7e9977a43.png)
> 参考：[为什么切片底层就是引用了还需要传切片指针？](https://juejin.cn/post/6899676873431253006)

**函数内扩容例子**
```go
func modifySlice(innerSlice []string) {
    innerSlice[0] = "b"
        innerSlice = append(innerSlice, "a") //触发扩容，后面的修改在新的数组了
    innerSlice[1] = "b"
    fmt.Println(innerSlice)
}

func main() {
    outerSlice := []string{"a", "a"}
     modifySlice(outerSlice)
    fmt.Println(outerSlice)
}
//输出
//[b b a]
//[b a]

func modifySlice(innerSlice []string) {
    innerSlice = append(innerSlice, "a")
    innerSlice[0] = "b"
    innerSlice[1] = "b"
    fmt.Println(innerSlice)
}

func main() {
    outerSlice:= make([]string, 0, 3)
    outerSlice = append(outerSlice, "a", "a")
     modifySlice(outerSlice)
    fmt.Println(outerSlice)
}
//输出
//[b b a]
//[b b]  虽然内外层都指向的同一底层数组，但outerslice的len值一直为2，所以只有两个值
```
**为什么a b的值不会交换？**
```go
func main(){
    a := []int{0, 0, 0}
    b := []int{1, 1, 1}
    fmt.Println("a:", a, "b:", b) // "a:[0,0,0] b:[1,1,1]"
    switchArray(a, b)
    fmt.Println("a:", a, "b:", b) //"a:[0,0,0] b:[1,1,1]"
}
func switchArray(a, b []int) {
    var c []int    
    c = a
    a = b
    b = c
}
```
首先，**go是值传递，函数内的a,b是函数外a,b的复制**，和函数外的a,b底层指向同一数组。所以，当函数内的a指针或者b指针变化，指向其他数组，并**不影响函数外指针的指向**，因此值不会交换。
## 字符

# 字符与字符串
## string
## string[i]能够赋值吗
- string是不能够修改的,因为其**底层的byte数组的只读的**
- 若要修改string，只能够新建string。例如s+="newstr"，本质就是新建了string。
## string和[]byte互转
```go
// string 转为[]byte
var str string = "test"
var data []byte = []byte(str)
//byte转为string
var data [10]byte
byte[0] = 'T'
byte[1] = 'E'
var str string = string(data[:])
```
## byte和rune
- byte 表示一个字节，rune 表示四个字节。因为rune是存储unicode编码的数据类型。
- [go 的 [] rune 和 [] byte 区别](https://learnku.com/articles/23411/the-difference-between-rune-and-byte-of-go)

## strings辅助包
常用方法：
- strings.Repeat("str",n)
- string.Join(数组，分隔符)
- 字符串和数字互转
```go
import strconv

//字符串转数字
strconv.Atoi()

//数字转字符串
strconv.Itoa()
```
## 字符串拼接对比
- [go字符串拼接方式及性能比拼](https://geektutu.com/post/hpg-string-concat.html)
- 五大方法
  - \+
    > 会开辟新的空间存储两个字符串
  - fmt.Sprintf
    > 接口参数，利用了反射，有性能损耗
  - strings.Builder
    > 通过writeString在sb. buf [] byte中拼接
  - string.Join
    > 基于strings.builder来实现的,并且可以自定义分隔符，能提前预分配buf的空间，减少了内存分配消耗的性能
  - bytes.Buffer
    > 通过Write在bytes.buffer底层的[]byte切片中拼接

