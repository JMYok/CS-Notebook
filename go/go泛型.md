# 泛型
## 类型形参、类型约束 、类型实参
```go
type MyMap[KEY int | string, VALUE float32 | float64] map[KEY]VALUE  
type Slice[T int|float32|float64 ] []T
```
## 如何使用呢？传递具体类型
```go
​var a Slice[int] = []int{1, 2, 3}
```
## 指定底层类型
```go
type Int interface {    ~int | ~int8 | ~int16 | ~int32 | ~int64}
​type MyInt int
```
~表示以int为底层结构的结构MyInt也能够作为泛型实参
## comparable和 ordered
```go
type MyMap[KEY comparable, VALUE any] map[KEY]VALUE
```
## comparable 可比较指的是?
可以执行 !=， == 操作的类型，并**没确保**这个类型可以执行大小比较（ >,<,<=,>= ）


