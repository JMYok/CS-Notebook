> go version: 1.22.2
# 定义
```go
// sync/mutex.go
type Mutex struct {
	state int32  // 4字节，互斥状态
	sema  uint32 // 4字节，控制锁状态的信号量
}
```
# 状态
![](https://pic.imgdb.cn/item/6697ca16d9c307b7e98691f5.png)
在默认情况下，互斥锁的所有状态位都是 0。
- waitersCount — 当前互斥锁上等待的 Goroutine 个数。
- mutexStarving — 当前的互斥锁进入饥饿状态；
- mutexWoken — 表示从正常模式被从唤醒；
- mutexLocked — 表示互斥锁的锁定状态；
## 正常模式和饥饿模式
- 在正常模式下，锁的等待者会按照先进先出的顺序获取锁。但是**刚被唤起的Goroutine与新创建的 Goroutine竞争时，大概率会获取不到锁**。
    > 1. 调度器的设计使得新创建的 goroutine 可能会比刚被唤醒的 goroutine 更快地获取执行机会
    > 2. 阻塞的 goroutine 会被唤醒并重新调度,这些系统调用带来的调度开销会导致唤醒的 goroutine 相对于新创建的 goroutine 更慢。
    > 3. Go 的调度器倾向于优先运行新创建的 goroutine，因为这些 goroutine 通常处于“就绪”状态，不需要经过复杂的调度操作。相比之下，刚被唤醒的 goroutine 可能需要更多的调度器操作来重新进入就绪状态。
- 为了减少这种情况的出现，**一旦 Goroutine 超过 1ms 没有获取到锁**，它就会将当前互斥锁切换饥饿模式，防止部分 Goroutine 被『饿死』。
- 饥饿模式是在 Go 语言在 1.9 中通过提交 [sync: make Mutex more fair](https://github.com/golang/go/commit/0556e26273f704db73df9e7c4c3d2e8434dec7be) 引入的优化，引入的目的是保证互斥锁的公平性。
  - 在饥饿模式中，**互斥锁会直接交给等待队列最前面的 Goroutine。新的 Goroutine 在该状态下不能获取锁、也不会进入自旋状态，它们只会在队列的末尾等待**。
  - 如果一个 Goroutine 获得了互斥锁并且它在队列的末尾或者它等待的时间少于1ms，那么当前的互斥锁就会切换回正常模式。

# 加锁解锁原理
