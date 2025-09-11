# 1. 接收者和参数看起来相似

比如这个方法：

```go
type Person struct {
    Name string
}

func (p Person) SayHello() {
    fmt.Println("Hello, my name is", p.Name)
}
```

完全可以写成普通函数：

```go
func SayHello(p Person) {
    fmt.Println("Hello, my name is", p.Name)
}
```

调用方式：

```go
p := Person{"Lewis"}
p.SayHello()      // 方法调用
SayHello(p)       // 函数调用
```

👉 这两个本质上是等价的，**接收者只是语法糖**。

------

# 2. 接收者的特殊作用

### (1) 语法糖：更像面向对象

- `p.SayHello()` 比 `SayHello(p)` 可读性更强，直观表达“p 这个对象能干什么”。
- 让类型的行为和数据绑定在一起。

------

### (2) 可以为类型定义行为

- Go 没有 `class`，所以不能直接在类里写方法。

- 但 Go 允许“给类型加方法”。

- 不仅结构体，**任意自定义类型**都能定义方法：

  ```go
  type MyInt int
  
  func (m MyInt) IsPositive() bool {
      return m > 0
  }
  
  func main() {
      var x MyInt = 5
      fmt.Println(x.IsPositive()) // true
  }
  ```

👉 普通函数做不到这一点：你不能写 `IsPositive(x)` 而让它“看起来像是 MyInt 自带的方法”。  

这里联想对比 class，在 java 中，我们可以给class 设定相关的方法，比如“苹果”类，可以有煎苹果，煮苹果方法。但是在 go 语言中，我们没有 class 这个概念，所以我们只能将苹果定义为类型(包含结构体/普通整型这类)，给苹果添加诸如煎苹果这种方法，就是我们所说的方法，所以一个接受者（苹果）可以有很多的方法。

------

### (3) 统一接口 (interface)

Go 的 **接口** 是通过“方法集合”定义的。

- 如果你只用普通函数，是没法让类型自动实现接口的。
- 必须用“接收者方法”，类型才能匹配接口。

例子：

```go
type Speaker interface {
    SayHello()
}

type Person struct{ Name string }

// 定义接收者方法
func (p Person) SayHello() {
    fmt.Println("Hello,", p.Name)
}

func main() {
    var s Speaker
    s = Person{"Lewis"} // ✅ Person 自动实现了 Speaker
    s.SayHello()
}
```

👉 如果用函数 `SayHello(p Person)`，`Person` 就不会实现 `Speaker`。

------

### (4) 接收者支持指针和值的区分

- **值接收者**：方法里修改不会影响原对象。
- **指针接收者**：方法里可以直接修改原对象。

```go
type Counter struct{ n int }

func (c Counter) AddOne()   { c.n++ }  // 值接收者
func (c *Counter) AddOne2() { c.n++ }  // 指针接收者

func main() {
    c := Counter{}
    c.AddOne()
    fmt.Println(c.n) // 0（副本被改了，原始没变）

    c.AddOne2()
    fmt.Println(c.n) // 1（原始被改了）
}
```

👉 普通函数也能用指针参数实现同样效果，但接收者方式更直观：`c.AddOne2()`。

------

# 3. 接收者 vs 参数的区别总结

| 特点       | 接收者 (receiver)                                | 普通参数                                    |
| ---------- | ------------------------------------------------ | ------------------------------------------- |
| 语法       | `func (p Person) MethodName()`                   | `func FuncName(p Person)`                   |
| 调用方式   | `p.MethodName()`                                 | `FuncName(p)`                               |
| 适用场景   | 给类型定义方法，使其具备行为；实现接口           | 通用逻辑处理                                |
| 可见性     | 方法属于类型的“方法集”                           | 不属于任何类型                              |
| 指针/值    | 可以区分值接收者和指针接收者，决定是否修改原对象 | 参数传值 / 指针本质一样，但接口需要方法形式 |
| 与接口关系 | 必须用方法才能匹配接口                           | 函数不行                                    |

------

# 一句话总结

- **接收者本质就是参数**，只是换了一种写法（`p.Method()` 代替 `Method(p)`）。
- 但 **接收者有额外语义**：让类型拥有“方法集”，从而能实现接口、模拟面向对象的行为。
- 如果你只是想写个工具函数 → 用普通函数；
   如果你想定义“某个类型能干什么” → 用方法。