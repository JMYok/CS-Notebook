# 数据结构
## go模拟stack
```go
st := make([]type,0)
//新增 模拟push
st = append(a,x)
//删除 模拟pop
x, st = st[len(st)-1], st[:len(st)-1]
```
## go 模拟stack（更少切片扩容）
```go
const N = 10tt := 0 //指向栈顶，不是栈顶下一个数；tt表示栈的大小
stack := make([]int, N)

// 入栈
tt++
stack[tt] = 66
tt++
stack[tt] = 88

//出栈
tt--
fmt.Println(stack[tt])

//不为空if 
tt > 0 {
    fmt.Println("stack不为空")
}
```

## go模拟queue
```go
a := make([]type,0)
//新增 模拟push
a = append(a,x)
//删除 模拟pop
x, a = a[0], a[1:]
```

## 其余操作
[go 用slice模拟vector功能 - Go语言中文网 - Golang中文社区
（每行输入字符和数字的情况](https://studygolang.com/articles/9443)

## 并查集
``` go
package main

import "fmt"

var father []int;

var M,N int

func FindFather(x int)int{
    if x==father[x]{
        return x
    }else{
        father[x]=FindFather(father[x]);
        return father[x]
    }
}

func Union(a int, b int){
    fa := FindFather(a);
    fb := FindFather(b);
    if fa != fb{
        father[fa] = fb;
    }
}


func main(){
    fmt.Scanln(&M,&N)  
    father=make([]int,N+1)
    for i:=1;i<=N;i++{
        father[i]=i
    }
}
```

# 输入输出
## ACM模式输入输出
[输入输出模板(Golang版)|ACM模式 - 掘金](https://juejin.cn/post/7129363764247773197)
### 行数固定，每行数据不固定
注意scanner初始化要在循环外面
``` go
package main

import (
    "bufio"
    "fmt"
    "os"
    "strconv"
    "strings"
)

func main() {
    sc := bufio.NewScanner(os.Stdin)
    for sc.Scan() {  //每次读入一行
        strs := strings.Split(sc.Text(), " ")  //通过空格将他们分割，并存入一个字符串切片
        ...
    }
}
```
### 输入一行数据
```go
package main

import "fmt"

func main(){
    var strByte []byte
    fmt.Scanln(&strByte) //可读入多个数
    ...
    }
```
# 字符串相关
## 判断大小写
### 通常的办法
```go
package main
import "fmt"
func main() {
    cntLower := 0    
    cntUpper := 0    
    s := "Hello World"    
    for i := 0; i < len(s); i++ {
       if s[i] >= 'a' && s[i] <= 'z' {
          cntLower++
       } else if s[i] >= 'A' && s[i] <= 'Z' {
          cntUpper++
       }
    }
    fmt.Println(cntLower)
    fmt.Println(cntUpper)
}
```
## 大数运算
### 大数减法
将字符数组转换为[]int数组
```go
func main(){
    var stra,strb string
    fmt.Scan(&stra,&strb)
    
    var a,b []int
    
    for i:=len(stra)-1;i>=0;i--{
        a = append(a,int(stra[i]-'0'))
    }
    for i:=len(strb)-1;i>=0;i--{
        b = append(b,int(strb[i]-'0'))
    }
    
    var flag int
    var ans []int
    if cmp(a,b){
        ans =sub(a,b)
    }else{
        flag = -1
        ans = sub(b,a)
    }
    if flag==-1{
        fmt.Print("-")
    }
    flag=0
    for i:=len(ans)-1;i>=0;i--{
        if ans[i]==0 && flag==0{
            continue
        }
        fmt.Print(ans[i])
        flag=1
    }
    if flag==0{
        fmt.Print("0")
    }
}

func sub(a,b []int) (c []int){
    for i,t:=0,0;i<len(a);i++{
        t = a[i]-t
        if i<len(b){
            t-=b[i]
        }
        c = append(c, (t+10)%10)
        if t<0{
            t = 1
        }else{
            t =0
        }
    }
    return
}

【0，1，2】   

func cmp(a,b []int) bool{
    if len(a)!=len(b){
        return len(a)>len(b)
    }
    
    for i:=len(a)-1;i>=0;i--{
        if a[i]!=b[i]{
            return a[i]>b[i]
        }
    }
    return true
}
```
### 使用Unicode包
```go
package main
import (
    "fmt"    
    "unicode"
)

func main() {
    cntLower := 0    
    cntUpper := 0    
    s := "Hello World"    
    for _, c := range s {
       if unicode.IsLower(c) {
          cntLower++
       } else if unicode.IsUpper(c) {
          cntUpper++
       }
    }
    fmt.Println(cntLower)
    fmt.Println(cntUpper)
}
```
# 树
### 树遍历
栈模拟树前序遍历：
- [114. 二叉树展开为链表](https://leetcode.cn/problems/flatten-binary-tree-to-linked-list)
```go
 node := root
    for node != nil || len(stack) > 0 {
        for node != nil {
            list = append(list, node)
            stack = append(stack, node)
            node = node.Left
        }
        node = stack[len(stack)-1]
        node = node.Right
        stack = stack[:len(stack)-1]
    }
```

# 图论
## 邻接表构图
```go
// 对于每个点k，开一个单链表，存储k所有可以走到的点。h[k]存储这个单链表的头结点
var h[N], e[N], ne[N], idx int;

// 添加一条边a->b
func add(a int, b int){
    e[idx] = b,
    ne[idx] = h[a]
    h[a] = idx++
}

// 初始化
for i:= range h{
    h[i]=-1
}
```

## 基于领接链表的DFS
```go
q:=make([]type,0)
st[1] = true
q = append(q,1)

for len(q)>0{
    x,q:=q[0],q[1:]
    for i:=h[t];i!=-1;i=ne[i]{
        j:=e[j]
        if(st[j]==false){
            st[j]=true
            q = append(q,j)
        }
    }
}
```
最短路

todop

# 数学问题
## 快速幂（各个运算都取余）
```go
func qmi(m,k,p int) int{
    res:=1%p
    t:=m
    for k>0{
        if k&1 == 1{
            res = res * t % p
        }
        t = t * t % p
        k>>=1
    }
    return res
}
快速幂
func pow(x,n int) int{
    res:=1
    for n>0{
        if n&1==1{
            res*=x
        }
        x*=x
        n = n>>1
    }
    return res
}
```
# 查找
## 二分查找
- [灵神思路](https://www.bilibili.com/video/BV1AP41137w7)
- 闭区间写法:核心在利用循环不变量。下图为返回第一个>=8的数下标 的循环不变量
![](https://pic.imgdb.cn/item/6665841b5e6d1bfa05c6abe6.png)
二分查找四种类型:
- \>=x（下面算法）
- \>x：转化为 >=x+1
- <x：转化为 >=x  - 1
- <=x: 转化为 >x - 1
```go
func search(nums []int, target int) int {
    left,right:=0,len(nums)-1
    for left<=right{
        mid:=left + (right-left)>>2
        if nums[mid]<target{
            left = mid+1
        }else{ //nums[mid]>=target,可以确定nums[mid~n-1]>=target，所以向左搜索
            right = mid-1
        }
    }
    if left>=len(nums)||nums[left]!= target{
        return -1
    }
    return left
}
```
# 排序
## 排序算法
[八大排序算法的Golang实现](https://juejin.cn/post/7066728890575618078)
### 冒泡排序
```go
func bubbleSort(array []int) []int {
    n := len(array)
    sortBorder:= n - 1    
    // 上次交换的下标    
    lastIdx := sortBorder
    for range array {
       isSorted := true       
       for j := 0; j < sortBorder; j++ {
          // 从小到大排序，每次选出一个当前的最大值          
          if array[j] > array[j+1] {
             array[j], array[j+1] = array[j+1], array[j]
             isSorted = false             
             lastIdx = j
          }
       }
       sortBorder = lastIdx
       if isSorted {
              break       
          }
    }
    return array
}
```
### 堆排序
```go
// **************** 堆排序 从大到小****************
func heapSort(array []int) []int {
    //小顶堆    
    createMinHeap(array)
    for i := len(array) - 1; i > 1; i-- {
       array[1], array[i] = array[i], array[1]
       downAdjust(1, i-1, array)
    }
    return array
}

func createMinHeap(array []int) {
    for i := len(array) / 2; i >= 1; i-- {
       downAdjust(i, len(array)-1, array)
    }
}

// 向下调整(小顶堆)
func downAdjust(low, high int, heap []int) {
    i := low
    j := 2 * i
    for j <= high {
       if j+1 <= high && heap[j+1] < heap[j] {
          j++
       }
       if heap[i] > heap[j] {
          heap[i], heap[j] = heap[j], heap[i]
          i = j
          j = 2 * i
       } else {
          break       
          }
    }
}
```
### 快速排序
```go
// **************** 快速排序 从小到大***********
func quickSort(array []int, startIndex int, endIndex int) {
    if startIndex >= endIndex {
       return    }
    pivotIdx := partition(array, startIndex, endIndex)
    quickSort(array, startIndex, pivotIdx-1)
    quickSort(array, pivotIdx+1, endIndex)
}

func partition(array []int, startIndex int, endIndex int) int {
    // 选第一个数为主元    
    pivot := array[startIndex]
    //记录当前没有数字的坑位，也是最终主元的位置    
    index := startIndex
    left, right := startIndex, endIndex

    for left <= right {
       //从右到左找小于pivot的数       
       for left <= right {
          if array[right] < pivot {
             array[left], array[right] = array[right], array[left]
             index = right
             right--
             break          
             }
          right--
       }

       //从左到右找大于pivot的数       
       for left <= right {
          if array[left] >= pivot {
             array[left], array[right] = array[right], array[left]
             index = left
             left++
             break          
             }
          left++
       }
    }
    array[index] = pivot
    return index
}
```

### 自定义排序逻辑
```go
type Sorter interface {
    Len() int
    Less(i, j int) bool
    Swap(i, j int)
}

type pair struct {
    target  string
    visited bool
}
type pairs []*pair

func (p pairs) Len() int {
    return len(p)
}
func (p pairs) Swap(i, j int) {
    p[i], p[j] = p[j], p[i]
}
func (p pairs) Less(i, j int) bool {
    return p[i].target < p[j].target
}
```
# DP
## 01背包、完全背包
### 题目变形
![](https://pic.imgdb.cn/item/66864e1ad9c307b7e93a84c5.png)
- i表示从前i个数中取值，j代表容量大小
- 以下例子代表递推写法
完全背包的区别就在于选过的数还能继续选，所以状态转移方程为：
![](https://pic.imgdb.cn/item/66864e43d9c307b7e93b22af.png)
### 恰好装capacity
#### 求方案数
- [494. 目标和](https://leetcode.cn/problems/target-sum/)、[322. 零钱兑换](https://leetcode.cn/problems/coin-change)
- 初始值：f[0][0]=1
- 状态转移：f(i,j) = f(i-1,j)+f(i-1,j-v[i]) 或 f(i+1,j) = f(i,j)+f(i,j-v[i])
```go
 for i, x := range nums {
        for c := 0; c <= target; c++ {
            if c < x {
                f[i+1][c] = f[i][c]
            } else {
                f[i+1][c] = f[i][c] + f[i][c-x]
            }
        }
    }
```
#### 求最大值
- [2915. 和为目标值的最长子序列的长度](https://leetcode.cn/problems/length-of-the-longest-subsequence-that-sums-to-target/)
- 初始值：f[0][x] = MIN_INT，理解为没有物品可以选；f[0][0]=0，没有物品可以选但target为0，所以最大值为0.
- 状态转移：f(i,j) = max(f(i-1,j),f(i-1,j-v[i]) +v[i])
```go
for i, x := range nums {
        for c := 0; c <= target; c++ {
            if c < x {
                f[i+1][c] = f[i][c]
            } else {
                f[i+1][c] = max(f[i][c] , f[i][c-x]+x)
            }
        }
    }
```
#### 求最小值
- 和最大值差不多，就是初始值改为MAXINT/2，避免溢出。
- 用min来转移状态
### 至多装capacity
``` python
@cache
def dfs(i,c):
    if i<0:
        return 1  # 不需要判断c==0,也就是不需要恰好等于capacity
    if c<nums[i]:
        return dfs(i-1,c)
    return dfs(i-1,c)+dfs(i-1,c-nums[i])
```
### 至少装capacity
``` python
@cache
def dfs(i,c):
    if i<0:
        return 1  if c<=0 else 0
    # 不需要判断c < nums[i]
    return dfs(i-1,c)+dfs(i-1,c-nums[i])
```
### 一维遍历顺序的理解
> **简记：01正序，完全背包倒序**
![](https://pic.imgdb.cn/item/66864f49d9c307b7e93f5984.png)
- 如果状态从这两个区域转移过来，就是倒序（例如01背包）；反之，就是正序（例如完全背包）。
- 从二维递推式来理解，例如01背包，更新f[c]的值需要的是当前f[c]和上一个状态的f[c-w]，**因为我们现在之后一个数组，若是正序，f[c-w]就更新过了，也就不是上一个状态的值了，所以必须逆序**； 若是完全背包，更新f[c]的值需要的是当前f[c]和当前状态的f[c-w]，需要的就是更新过的值，所以正序是没问题的。

### 01背包递推值理解
拿494. 目标和的第一组数据来举例子nums = [1,1,1,1,1], target = 4。
f[i+1][j] = f[i][j]+f[i][j-x]
![](https://pic.imgdb.cn/item/66864fa9d9c307b7e940a989.png)

若去掉一维:
- f[j] = f[j] + f[j-x]，外层的遍历for i, x := range nums表示当前数组的状态。
- 也就是虽然只有一个数组，但是这个数组还是有状态的，当从i遍历到i+1时，刚开始数组还是i的状态，如果正序遍历会把i的状态值覆盖，此时更新i+1状态时就丢失了i状态的值。
- **以上图表格为例子，黄色格子应该使用上一状态绿色格子的值，但是由于只有一个数组，就只能使用红色格子的值了，也就丢失了绿色格子的值。**






