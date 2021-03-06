# Go 程序的基本结构和要素

- [Go 程序的基本结构和要素](#go-程序的基本结构和要素)
  - [文件名、关键字与标识符](#文件名关键字与标识符)
  - [包的概念、导入与可见性](#包的概念导入与可见性)
  - [函数](#函数)
  - [注释](#注释)
  - [类型](#类型)
  - [Go 程序的一般结构](#go-程序的一般结构)
  - [类型转换](#类型转换)
  - [Go 命名规范](#go-命名规范)
- [参考资料](#参考资料)

## 文件名、关键字与标识符

Go 的源文件以 `.go` 为后缀名存储在计算机中，这些文件名均由小写字母组成，如 `scanner.go` 。如果文件名由多个部分组成，则使用下划线 `_` 对它们进行分隔，如 `scanner_test.go` 。文件名不包含空格或其他特殊字符。

一个源文件可以包含任意多行的代码，Go 本身没有对源文件的大小进行限制。

你会发现在 Go 代码中的几乎所有东西都有一个名称或标识符。另外，Go 语言也是区分大小写的，这与 C 家族中的其它语言相同。有效的标识符必须以字母（可以使用任何 UTF-8 编码的字符或 _）开头，然后紧跟着 0 个或多个字符或 Unicode 数字，如：X56、group1、_x23、i、өԑ12。

以下是无效的标识符：

- 1ab（以数字开头）
- case（Go 语言的关键字）
- a+b（运算符是不允许的）
  
`_` 本身就是一个特殊的标识符，被称为空白标识符。它可以像其他标识符那样用于变量的声明或赋值（任何类型都可以赋值给它），但任何赋给这个标识符的值都将被抛弃，因此这些值不能在后续的代码中使用，也不可以使用这个标识符作为变量对其它变量进行赋值或运算。

在编码过程中，你可能会遇到没有名称的变量、类型或方法。虽然这不是必须的，但有时候这样做可以极大地增强代码的灵活性，这些变量被统称为匿名变量。

下面列举了 Go 代码中会使用到的 25 个关键字或保留字：

||||||
------|-----------|-------|-----------|-------
break |	default	| func  | interface | select
case  |	defer    |	go   | map	     | struct
chan  |	else     |	goto | package   | switch
const	| fallthrough	     | if	     | range	| type
continue	         | for	  | import    | return	| var

之所以刻意地将 Go 代码中的关键字保持的这么少，是为了简化在编译过程第一步中的代码解析。和其它语言一样，关键字不能够作标识符使用。

除了以上介绍的这些关键字，Go 语言还有 36 个预定义标识符，其中包含了基本类型的名称和一些基本的内置函数

||||||||||
------------|-----------|-------------|---------|---------|---------|-----------|-------------|-------
append	   | bool	   | byte        | cap	   | close	 | complex | complex64 | complex128  | uint16
copy        | false	   | float32     | float64 | imag    |	int	  | int8      | int16       |	uint32
int32	      | int64	   | iota	     | len     | make    |	new     | nil       | panic       |	uint64
print	      | println	| real	     | recover |	string | true	  | uint	     | uint8	    | uintptr

程序一般由关键字、常量、变量、运算符、类型和函数组成。

程序中可能会使用到这些分隔符：括号 `()`，中括号 `[]` 和大括号 `{}`。

程序中可能会使用到这些标点符号：`.`、`,`、`;`、`:` 和 `…`。

程序的代码通过语句来实现结构化。每个语句不需要像 C 家族中的其它语言一样以分号 `;` 结尾，因为这些工作都将由 Go 编译器自动完成。

如果你打算将多个语句写在同一行，它们则必须使用 `;` 人为区分，但在实际开发中我们并不鼓励这种做法。

## 包的概念、导入与可见性

包是结构化代码的一种方式：每个程序都由包（通常简称为 pkg）的概念组成，可以使用自身的包或者从其它包中导入内容。

如同其它一些编程语言中的类库或命名空间的概念，每个 Go 文件都属于且仅属于一个包。一个包可以由许多以 .go 为扩展名的源文件组成，因此文件名和包名一般来说都是不相同的。

你必须在源文件中非注释的第一行指明这个文件属于哪个包，如：package main。package main表示一个可独立执行的程序，每个 Go 应用程序都包含一个名为 main 的包。

一个应用程序可以包含不同的包，而且即使你只使用 main 包也不必把所有的代码都写在一个巨大的文件里：你可以用一些较小的文件，并且在每个文件非注释的第一行都使用 package main 来指明这些文件都属于 main 包。如果你打算编译包名不是为 main 的源文件，如 pack1，编译后产生的对象文件将会是 pack1.a 而不是可执行程序。另外要注意的是，所有的包名都应该使用小写字母。

**标准库**

在 Go 的安装文件里包含了一些可以直接使用的包，即标准库。在 Windows 下，标准库的位置在 Go 根目录下的子目录 pkg\windows_386 中；在 Linux 下，标准库在 Go 根目录下的子目录 pkg\linux_amd64 中（如果是安装的是 32 位，则在 linux_386 目录中）。一般情况下，标准包会存放在 $GOROOT/pkg/$GOOS_$GOARCH/ 目录下。

Go 的标准库包含了大量的包（如：fmt 和 os），但是你也可以创建自己的包（第 9 章）。

如果想要构建一个程序，则包和包内的文件都必须以正确的顺序进行编译。包的依赖关系决定了其构建顺序。

属于同一个包的源文件必须全部被一起编译，一个包即是编译时的一个单元，因此根据惯例，每个目录都只包含一个包。

如果对一个包进行更改或重新编译，所有引用了这个包的客户端程序都必须全部重新编译。

Go 中的包模型采用了显式依赖关系的机制来达到快速编译的目的，编译器会从后缀名为 .o 的对象文件（需要且只需要这个文件）中提取传递依赖类型的信息。

如果 A.go 依赖 B.go，而 B.go 又依赖 C.go：

- 编译 C.go, B.go, 然后是 A.go.
- 为了编译 A.go, 编译器读取的是 B.o 而不是 C.o.

这种机制对于编译大型的项目时可以显著地提升编译速度。

**包的导入**

一个 Go 程序是通过 import 关键字将一组包链接在一起。

`import "fmt"` 告诉 Go 编译器这个程序需要使用 fmt 包（的函数，或其他元素），fmt 包实现了格式化 IO（输入/输出）的函数。包名被封闭在半角双引号 "" 中。如果你打算从已编译的包中导入并加载公开声明的方法，不需要插入已编译包的源代码。

如果需要多个包，它们可以被分别导入：
```go
import "fmt"
import "os"
```
或
```go
import "fmt"; import "os"
```
但是还有更短且更优雅的方法（被称为因式分解关键字，该方法同样适用于 `const`、`var` 和 `type` 的声明或定义）：
```go
import (
   "fmt"
   "os"
)
```

**可见性规则**

当标识符（包括常量、变量、类型、函数名、结构字段等等）以一个大写字母开头，如：Group1，那么使用这种形式的标识符的对象就可以被外部包的代码所使用（客户端程序需要先导入这个包），这被称为导出（像面向对象语言中的 public）；标识符如果以小写字母开头，则对包外是不可见的，但是他们在整个包的内部是可见并且可用的（像面向对象语言中的 private ）。

（大写字母可以使用任何 Unicode 编码的字符，比如希腊文，不仅仅是 ASCII 码中的大写字母）。

因此，在导入一个外部包后，能够且只能够访问该包中导出的对象。

假设在包 pack1 中我们有一个变量或函数叫做 Thing（以 T 开头，所以它能够被导出），那么在当前包中导入 pack1 包，Thing 就可以像面向对象语言那样使用点标记来调用：pack1.Thing（pack1 在这里是不可以省略的）。

因此包也可以作为命名空间使用，帮助避免命名冲突（名称冲突）：两个包中的同名变量的区别在于他们的包名，例如 pack1.Thing 和 pack2.Thing。

你可以通过使用包的别名来解决包名之间的名称冲突，或者说根据你的个人喜好对包名进行重新设置，如：import fm "fmt"。下面的代码展示了如何使用包的别名：

```go
package main

import fm "fmt" // alias3

func main() {
   fm.Println("hello, world")
}
```

## 函数

这是定义一个函数最简单的格式：
```go
func functionName()
```

你可以在括号 () 中写入 0 个或多个函数的参数（使用逗号 , 分隔），每个参数的名称后面必须紧跟着该参数的类型。

main 函数是每一个可执行程序所必须包含的，一般来说都是在启动后第一个执行的函数（如果有 init() 函数则会先执行该函数）。如果你的 main 包的源代码没有包含 main 函数，则会引发构建错误 undefined: main.main。main 函数既没有参数，也没有返回类型（与 C 家族中的其它语言恰好相反）。如果你不小心为 main 函数添加了参数或者返回类型，将会引发构建错误：
```
func main must have no arguments and no return values results.
```
在程序开始执行并完成初始化后，第一个调用（程序的入口点）的函数是 main.main()（如：C 语言），该函数一旦返回就表示程序已成功执行并立即退出。

函数里的代码（函数体）使用大括号 {} 括起来。

左大括号 { 必须与方法的声明放在同一行，这是编译器的强制规定，否则你在使用 gofmt 时就会出现错误提示：
```
`build-error: syntax error: unexpected semicolon or newline before {`
```
（这是因为编译器会产生 func main() ; 这样的结果，很明显这错误的）

**Go 语言虽然看起来不使用分号作为语句的结束，但实际上这一过程是由编译器自动完成。** 因此才会引发像上面这样的错误

右大括号 } 需要被放在紧接着函数体的下一行。如果你的函数非常简短，你也可以将它们放在同一行：
```go
func Sum(a, b int) int { return a + b }
```

对于大括号 {} 的使用规则在任何时候都是相同的（如：if 语句等）。

因此符合规范的函数一般写成如下的形式：
```go
func functionName(parameter_list) (return_value_list) {
   …
}
```
其中：

`parameter_list` 的形式为 `(param1 type1, param2 type2, …)`
`return_value_list` 的形式为 `(ret1 type1, ret2 type2, …)`
只有当某个函数需要被外部包调用的时候才使用大写字母开头，并遵循 Pascal 命名法；否则就遵循骆驼命名法，即第一个单词的首字母小写，其余单词的首字母大写。

下面这一行调用了 fmt 包中的 Println 函数，可以将字符串输出到控制台，并在最后自动增加换行字符 \n：
```go
fmt.Println（"hello, world"）
```
使用 `fmt.Print("hello, world\n")` 可以得到相同的结果。

`Print` 和 `Println` 这两个函数也支持使用变量，如：`fmt.Println(arr)`。如果没有特别指定，它们会以默认的打印格式将变量 `arr` 输出到控制台。

单纯地打印一个字符串或变量甚至可以使用预定义的方法来实现，如：`print`、`println`：`print("ABC")`、`println("ABC")`、`println(i)`（带一个变量 i）。

这些函数只可以用于调试阶段，在部署程序的时候务必将它们替换成 fmt 中的相关函数。

当被调用函数的代码执行到结束符 } 或返回语句时就会返回，然后程序继续执行调用该函数之后的代码。

## 注释
```go
package main

import "fmt" // Package implementing formatted I/O.

func main() {
   fmt.Printf("Καλημέρα κόσμε; or こんにちは 世界\n")
}
```
上面这个例子通过打印 Καλημέρα κόσμε; or こんにちは 世界 展示了如何在 Go 中使用国际化字符，以及如何使用注释。

注释不会被编译，但可以通过 godoc 来使用。

单行注释是最常见的注释形式，你可以在任何地方使用以 // 开头的单行注释。多行注释也叫块注释，均已以 /* 开头，并以 */ 结尾，且不可以嵌套使用，多行注释一般用于包的文档描述或注释成块的代码片段。

每一个包应该有相关注释，在 package 语句之前的块注释将被默认认为是这个包的文档说明，其中应该提供一些相关信息并对整体功能做简要的介绍。一个包可以分散在多个文件中，但是只需要在其中一个进行注释说明即可。当开发人员需要了解包的一些情况时，自然会用 godoc 来显示包的文档说明，在首行的简要注释之后可以用成段的注释来进行更详细的说明，而不必拥挤在一起。另外，在多段注释之间应以空行分隔加以区分。

示例：
```go
// Package superman implements methods for saving the world.
//
// Experience has shown that a small number of procedures can prove
// helpful when attempting to save the world.
package superman
```
几乎所有全局作用域的类型、常量、变量、函数和被导出的对象都应该有一个合理的注释。如果这种注释（称为文档注释）出现在函数前面，例如函数 Abcd，则要以 "Abcd..." 作为开头。

示例：
```go
// enterOrbit causes Superman to fly into low Earth orbit, a position
// that presents several possibilities for planet salvation.
func enterOrbit() error {
   ...
}
```
godoc 工具会收集这些注释并产生一个技术文档。

## 类型

变量（或常量）包含数据，这些数据可以有不同的数据类型，简称类型。使用 var 声明的变量的值会自动初始化为该类型的零值。类型定义了某个变量的值的集合与可对其进行操作的集合。

类型可以是基本类型，如：`int、float、bool、string`；结构化的（复合的），如：`struct、array、slice、map、channel`；只描述类型的行为的，如：`interface`。

结构化的类型没有真正的值，它使用 `nil` 作为默认值（在 Objective-C 中是 `nil`，在 Java 中是 `null`，在 C 和 C++ 中是`NULL`或 0）。值得注意的是，Go 语言中不存在类型继承。

函数也可以是一个确定的类型，就是以函数作为返回类型。这种类型的声明要写在函数名和可选的参数列表之后，例如：
```go
func FunctionName (a typea, b typeb) typeFunc
```
你可以在函数体中的某处返回使用类型为 typeFunc 的变量 var：
```go
return var
```
一个函数可以拥有多返回值，返回类型之间需要使用逗号分割，并使用小括号 () 将它们括起来，如：
```go
func FunctionName (a typea, b typeb) (t1 type1, t2 type2)
```
示例： 函数 `Atoi: func Atoi(s string) (i int, err error)`

返回的形式：
```go
return var1, var2
```
这种多返回值一般用于判断某个函数是否执行成功（`true/false`）或与其它返回值一同返回错误消息（详见之后的并行赋值）。

使用 `type` 关键字可以定义你自己的类型，你可能想要定义一个结构体，但是也可以定义一个已经存在的类型的别名，如：
```go
type IZ int
```
**这里并不是真正意义上的别名，因为使用这种方法定义之后的类型可以拥有更多的特性，且在类型转换时必须显式转换。**

然后我们可以使用下面的方式声明变量：
```go
var a IZ = 5
```
这里我们可以看到 `int` 是变量 `a` 的底层类型，这也使得它们之间存在相互转换的可能。

如果你有多个类型需要定义，可以使用因式分解关键字的方式，例如：
```go
type (
   IZ int
   FZ float64
   STR string
)
```
每个值都必须在经过编译后属于某个类型（编译器必须能够推断出所有值的类型），因为 Go 语言是一种静态类型语言。

## Go 程序的一般结构

下面的程序可以被顺利编译但什么都做不了，不过这很好地展示了一个 Go 程序的首选结构。这种结构并没有被强制要求，编译器也不关心 main 函数在前还是变量的声明在前，但使用统一的结构能够在从上至下阅读 Go 代码时有更好的体验。

所有的结构将在这一章或接下来的章节中进一步地解释说明，但总体思路如下：

- 在完成包的 import 之后，开始对常量、变量和类型的定义或声明。
- 如果存在 init 函数的话，则对该函数进行定义（这是一个特殊的函数，每个含有该函数的包都会首先执行这个函数）。
- 如果当前包是 main 包，则定义 main 函数。
- 然后定义其余的函数，首先是类型的方法，接着是按照 main 函数中先后调用的顺序来定义相关函数，如果有很多函数，则可以按照字母顺序来进行排序。

```go
package main

import (
   "fmt"
)

const c = "C"

var v int = 5

type T struct{}

func init() { // initialization of package
}

func main() {
   var a int
   Func1()
   // ...
   fmt.Println(a)
}

func (t T) Method1() {
   //...
}

func Func1() { // exported function Func1
   //...
}
```
Go 程序的执行（程序启动）顺序如下：

1. 按顺序导入所有被 main 包引用的其它包，然后在每个包中执行如下流程：
2. 如果该包又导入了其它的包，则从第一步开始递归执行，但是每个包只会被导入一次。
3. 然后以相反的顺序在每个包中初始化常量和变量，如果该包含有 init 函数的话，则调用该函数。
4. 在完成这一切之后，main(包) 也执行同样的过程，最后调用 main 函数开始执行程序。

## 类型转换

在必要以及可行的情况下，一个类型的值可以被转换成另一种类型的值。由于 Go 语言不存在隐式类型转换，因此所有的转换都必须显式说明，就像调用一个函数一样（类型在这里的作用可以看作是一种函数）：
```go
valueOfTypeB = typeB(valueOfTypeA)
```
类型 B 的值 = 类型 B(类型 A 的值)

示例：
```go
a := 5.0
b := int(a)
```
但这只能在定义正确的情况下转换成功，例如从一个取值范围较小的类型转换到一个取值范围较大的类型（例如将 `int16` 转换为 `int32`）。当从一个取值范围较大的转换到取值范围较小的类型时（例如将 `int32` 转换为 `int16` 或将 `float32` 转换为 `int`），会发生精度丢失（截断）的情况。当编译器捕捉到非法的类型转换时会引发编译时错误，否则将引发运行时错误。

具有相同底层类型的变量之间可以相互转换：
```go
var a IZ = 5
c := int(a)
d := IZ(c)
```

## Go 命名规范

干净、可读的代码和简洁性是 Go 追求的主要目标。通过 gofmt 来强制实现统一的代码风格。Go 语言中对象的命名也应该是简洁且有意义的。像 Java 和 Python 中那样使用混合着大小写和下划线的冗长的名称会严重降低代码的可读性。名称不需要指出自己所属的包，因为在调用的时候会使用包名作为限定符。返回某个对象的函数或方法的名称一般都是使用名词，没有 `Get...` 之类的字符，如果是用于修改某个对象，则使用 `SetName`。有必须要的话可以使用大小写混合的方式，如 `MixedCaps` 或 `mixedCaps`，而不是使用下划线来分割多个名称。

# 参考资料
- [The Way To Go —— 文件名、关键字与标识符](https://github.com/unknwon/the-way-to-go_ZH_CN/blob/master/eBook/04.1.md)
- [The Way To Go —— Go 程序的基本结构和要素](https://github.com/unknwon/the-way-to-go_ZH_CN/blob/master/eBook/04.2.md)