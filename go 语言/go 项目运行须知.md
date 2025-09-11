## 1. 编译单位：包 (Package)

- Go 的编译单位是 **包**，不是单个 `.go` 文件。
- 同一目录下的所有 `.go` 文件（声明相同的 `package` 名）会被编译为一个整体。
- 如果只运行 `main.go`，而没把同目录下的其他文件一起编译，就会报 `undefined`。

------

## 2. 如何运行

- **运行整个包（推荐）**

  ```bash
  go run .
  ```

  编译并运行当前目录下所有 `.go` 文件。

- **指定多个文件**

  ```bash
  go run main.go array.go
  ```

- **编译再运行**

  ```bash
  go build .
  ./yourProgramName
  ```

------

## 3. main 包和入口函数

- 可执行程序的入口必须是：

  ```go
  package main
  
  func main() {
      // 程序入口
  }
  ```

- 如果没有 `main` 包或 `main` 函数，运行时会报错。

------

## 4. 包与目录结构

- **一个目录 = 一个包**。

- 常见项目结构：

  ```
  project/
  ├── main.go        (package main，程序入口)
  ├── utils/
  │   └── math.go    (package utils)
  └── model/
      └── user.go    (package model)
  ```

- 引用方式：

  ```go
  import "project/utils"
  import "project/model"
  ```

------

## 5. 常见坑点

1. **只运行单文件**
   - `go run main.go` 只编译 main.go → 可能报 `undefined`。
   - ✅ 正确方式：`go run .`
2. **目录下包名不一致**
   - 一个目录内的所有 `.go` 文件必须使用相同的 `package` 声明。
3. **循环 import**
   - 包 A import B，B 又 import A → 编译报错。
4. **未使用变量 / import**
   - Go 强制：有未使用的变量或 import 会导致编译失败。
5. **测试文件**
   - 以 `_test.go` 结尾的文件不会参与正常编译，但会在 `go test` 时运行。

------

## 6. 推荐习惯

- 运行项目时尽量用：

  ```bash
  go run .
  ```

- 一个目录只放一个包，不要混合多个包名。

- 函数/变量首字母大写 → 导出（跨包可见）；小写 → 包内可见。

- 避免“点导入”（`import . "xxx"`），防止命名冲突。

- 临时调试的 demo 文件也要保持 `package main`，否则容易报错。

