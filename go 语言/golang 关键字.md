### 25个关键词

```text
break       default     func        interface   select
case        defer       go          map         struct
chan        else        goto        package     switch
const       fallthrough if          range       type
continue    for         import      return      var
```



### defer

延迟执行某个函数，直到当前函数(在下面这个例子中表示 main)返回前才执行，对于一个函数里的多个 defer，他们会按照<mark>后进先出</mark>的顺序执行

```go
package main

import "fmt"

func main() {
    defer fmt.Println("defer 1")
    defer fmt.Println("defer 2")
    defer fmt.Println("defer 3")

    fmt.Println("main running...")
}

```

```go
main running...
defer 3
defer 2
defer 1
```

使用场景：资源释放，解锁，关闭文件/网络连接。一般在资源获取后，马上注册释放。<mark>防止忘记</mark>>

```go
f, _ := os.Open("data.txt")
defer f.Close()
```

### make

make 为内置函数，用于初始化三类引用类型(slice/map/channel)

| 类型  | 写法与参数                                | 说明                                        |
| ----- | ----------------------------------------- | ------------------------------------------- |
| slice | `make([]T, len)` 或 `make([]T, len, cap)` | 分配底层数组并返回切片；元素为零值          |
| map   | `make(map[K]V, hint)`                     | 创建可用 map；`hint` 是容量预估（非硬限制） |
| chan  | `make(chan T, n)`                         | 创建通道；`n=0` 为无缓冲，`n>0` 为有缓冲    |

### go

在当前函数中异步启动一个新的执行单元，让某个函数在''旁路''执行。

**特性要点**：

- **非阻塞**：`go f()` 立即返回，不等待 `f` 执行完。
- **不保证时序**：谁先开始、谁先结束都不保证；需要**同步手段**（channel、`sync.WaitGroup`、`Context` 等）来协调。
- **main 退出即全退**：`main()` 返回，整个进程就结束，**尚未结束的 goroutine 会被直接终止**。
- **panic 独立**：在 goroutine 里触发的 `panic` 不会被外层捕获，须在 goroutine 内部 `recover`。

<mark>闭包延迟问题</mark>

```go
// 错误：i 被闭包延迟读取，多个 goroutine 可能拿到同一个 i 最终值
for i := 0; i < 3; i++ {
    go func() { fmt.Println(i) }() // 可能输出 2,2,2
}

// 正确：为每次迭代创建局部拷贝
for i := 0; i < 3; i++ {
    i := i
    go func() { fmt.Println(i) }() // 稳定输出 0,1,2（顺序仍不保证）
}

```

为什么会错误，因为有可能外层执行的很快，即外层都将 i 的累加做完了，内层都还没有执行，因为内层读的 i 是来源于外层，如果外层执行完了内层都还没执行，那么三个 go 执行的读的都是外面的 2，局部拷贝可以解决这个问题。

### chan

用于声明和操作**通道**  

通道作用：多个 goroutine 之间安全地传输数据。  

**通过 make 创建通道**

```go
ch := make(chan int)       // 无缓冲通道
ch := make(chan int, 5)    // 带缓冲通道，容量 5
```

**发送数据与接收数据**

```go
ch <- 10 // 把 10 发送到通道
```

```go
x := <-ch // 从通道接收数据
```

**单向通道**

只读通道：<-chan int  

只写通道：chan-> int  

箭头方向决定只读还是只写

```go
func producer(ch chan<- int) {
    ch <- 100
}

func consumer(ch <-chan int) {
    fmt.Println(<-ch)
}

```

**缓冲 vs 无缓冲**

无缓冲写进去一个读出来一个，否则就阻塞。  

有缓冲会预设一个容量，只要不超过这个容量就不会阻塞。  

**关闭通道**

```go
close(ch)
```

关闭后不能再写入，否则 panic，但是如果 ch 中有剩余数据，可以读出来，如果没有数据，读出来是 0

```go
ch := make(chan int, 2)
ch <- 1
close(ch)

// ch 的返回值有两个，第一个值是通道里的值，第二个值是一个布尔值，表示能否成功读到数据（即通道是否是关闭的）
v, ok := <-ch
// v, _ := <-ch
// v := <-ch
fmt.Println(v, ok) // 1 true

v, ok = <-ch
fmt.Println(v, ok) // 0 false （空且关闭）
```

