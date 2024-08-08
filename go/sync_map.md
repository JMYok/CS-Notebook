# sync.Map
## 什么是sync.Map？
- 如果要实现并发访问map，通常是加锁，但性能很差
- sync.Map采用空间换时间的策略实现读写分离，利用read map处理读、更新、删除操作，当read map没有值或者写操作时再加锁🔒访问dirty map。
- 只有读读是并发安全的
![alt text](read_dirty_map.png)
