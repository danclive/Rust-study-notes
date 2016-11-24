chapter6 运算符
===============

Rust 中的运算符大部分和其他语言是一样的。

#### 一元运算符

顾名思义，一元操作符是专门对一个 Rust 元素进行操作的运算符，主要包括以下几个：

- `-` ：取负，专门用于数值类型。实现了 `std::ops::Neg`。
- `*` ：解引用。实现了 `std::ops::Deref` 或 `std::ops::DerefMut`。
- `!` ：取反。例如 `!false` 相当于 `true`。有意思的是，如果这个操作符对数字类型使用，会将其每一位都置反！也就是说，对一个 `1u8` 进行 `!` 操作，将得到一个 `254u8`。实现了 `std::ops::Not`。
- `&` 和 `&mut` ：租借，borrow。向一个 owner 租借其使用权，分别租借一个只读使用权和读写使用权。

#### 二元运算符

##### 算数操作符

- `+` ：加法。实现了 `std::ops::Add`。
- `-` ：减法。实现了 `std::ops::Sub`。
- `*` ：乘法。实现了 `std::ops::Mul`。
- `/` ：除法。实现了 `std::ops::Div`。
- `%` ：取余。实现了 `std::ops::Rem`。

##### 位运算符

- `&` ：与操作。实现了 `std::ops::BitAnd`。
- `|` ：或操作。实现了 `std::ops::BitOr`。
- `^` ：异或。实现了 `std::ops::BitXor`。
- `<<` ：左移运算符。实现了 `std::ops::Shl`。
- `>>` ：右移运算符。实现了 `std::ops::Shr`。

##### 惰性 boolean 运算符

逻辑运算符有三个，分别是 `&&`、`||` 和 `!`。其中前两个叫做惰性 boolean 运算符，之所以叫这个名字，是因为在 Rust 中也会出现其他类 C 语言的逻辑短路问题，所以取了这么一个名字。其作用和 C 语言里的一模一样。不过不同的是，Rust 里这个运算符只能用在 `bool` 类型上。

##### 比较运算符

比较运算符实际上也是某些 trait 的语法糖，不过比较运算符所实现的 trait 只有2个：`std::cmp::PartialEq` 和 `std::cmp::PartialOrd`。

其中，`==` 和 `!=` 实现的是 `PartialEq`，`<`、`>`、`>=` 和 `<=` 实现的是 `PartialOrd`。

标准库中，`std::cmp` 这个 mod 下有4个 trait，而且直观来看 `Ord` 和 `Eq` 岂不是更好？但 Rust 对于这4个 trait 的处理是很明确的。因为在浮点数有一个特殊的值叫 `NaN`，这个值表示未定义的一个浮点数。在 Rust 中可以用 `0.0f32 / 0.0f32` 来求得其值，这个数是一个都确定的值，但它表示的是一个不确定的数，那么 `NaN != NaN` 的结果是啥？标准库告诉我们是 `true`。但这么写有不符合 `Eq` 定义里的 `total equal` （每位一样两个数就一样）的定义。因此有了 `PartialEq` 这么一个定义，`NaN` 这个情况就给它特指了。

为了普适的情况，Rust 的编译器就选择了 `PartialOrd` 和 `PartialEq` 来作为其默认的比较符号的 trait。

#### 类型转换运算符

这个看起来并不算个运算符，因为它是个单词 `as`。就是类似于其他语言中的显示转换了。

```rust
fn avg(vals: &[f64]) -> f64 {
    let sum: f64 = sum(vals);
    let num: f64 = len(vals) as f64;
    sum / num
}
```

#### 重载运算符

上面说了很多 trait，就是为了运算符重载。Rust 是支持运算符重载的。更详细的部分，会在后续章节中介绍。这是一个例子：

```rust
use std::ops::{Add, Sub};

#[derive(Copy, Clone)]
struct A(i32);

impl Add for A {
    type Output = A;
    fn add(self, rhs: A) -> A {
        A(self.0 - rhs.0)
    }
}

impl Sub for A {
    type Output = A;
    fn sub(self, rhs: A) -> A{
        A(self.0 + rhs.0)
    }
}

fn main() {
    let a1 = A(10i32);
    let a2 = A(5i32);
    let a3 = a1 + a2;
    println!("{}", (a3).0);
    let a4 = a1 - a2;
    println!("{}", (a4).0);
}
```

```
    Finished debug [unoptimized + debuginfo] target(s) in 0.0 secs
     Running `yourpath\hello_world\target\debug\hello_world.exe`
5
15
```

#### 格式化字符串

Rust 采取了一种类似 Python 里面 format 的用法，其核心组成是5个宏和两个 trait ：
`format!`、`format_arg!`、`print!`、`println!`、`write！` 和 `Debug`、`Display`。

之前在 hello_world 里已经使用了 `print!` 或者 `println!` 这两个宏，但是最核心的是 `format!`，前两个宏只不过是将 `format!` 的结果输出到 `console` 而已。

先来分析一个 `format!` 的应用：

```rust
fn main() {
    let s = format!("今天是{0}年{1}月{2}日, {week:?}, 气温{3:>0width$} ~ {4:>0width$} 摄氏度。",
        2016, 11, 24, 3, -6, week = "Thursday", width = 2);

    print!("{}", s);
}
```

可以看到，`format!` 宏调用的时候参数可以是任意类型，而且可以 position 参数和 key-value 参数混合使用。但要注意一点，key-value 的值只能出现在 position 值之后并且不占用 position。比如把上面的代码改动一下：

```rust
fn main() {
    let s = format!("今天是{0}年{1}月{2}日, {week:?}, 气温{3:>0width$} ~ {4:>0width$} 摄氏度。",
        2016, 11, 24, week = "Thursday", 3, -6, width = 2);

    print!("{}", s);
}
```

这样将会报错：

```
   Compiling hello_world v0.1.0 (yourpath/hello_world)
error: expected ident, positional arguments cannot follow named arguments
 --> main.rs:3:42
  |
3 |         2016, 11, 24, week = "Thursday", 3, -6, width = 3);
  |                                          ^

error: aborting due to previous error

error: Could not compile `hello_world`.
```

还需要注意的是，参数类型必须要实现 `std::fmt` mod 下的某些 trait。比如原生类型大部分都实现了 `Display` 和 `Debug` 这两个宏，整数类型还额外实现了 `Binary`，等等。

可以通过 `{:type}` 的方式取调用这些参数。比如：

```rust
format!(":b", 2); // 调用 `Binary` trait

format!(":?", "hello"); // 调用 `Debug`
```

如果 type 为空的话默认调用 `Display`。

冒号 `:` 后面还有更多参数，比如上面代码中的 `{3:>0width$}` 和 `{4:>0width$}`。首先 `>` 是一个语义，它表示的是生成的字符串向右对齐，于是上面的代码得到了 `003` 和 `-06` 这两个值。与之相对的还有向左对齐 `<` 和居中 `^`。
接下来 `0` 是一种特殊的填充语法，他表示用 0 补齐数字的空位，而 `width&` 表示格式化后的字符串长度。它可以是一个精确的长度数值，也可以是一个以 `$` 为结尾的字符串，`$` 前面的部分可以写一个 key 或者一个 position。

还要注意的是，在 width 和 type 之间会有一个叫精度的区域，他们的表示通常是以 `.` 开始的，比如 `.4` 表示小数点后4位精度。最让人糟心的是，任然可以在这个位置引用参数，只需要个上面 width 一样，用 `.N$` 来表示一个 position 的参数，只是就是不能引用 key-value 类型的。比如：

```rust
fn main() {
    // Hello {arg 0 ("x")} is {arg 1 (0.01) with precision specified inline (5)}
    println!("Hello {0} is {1:.5}", "x", 0.01);

    // Hello {arg 1 ("x")} is {arg 2 (0.01) with precision specified in arg 0 (5)}
    println!("Hello {1} is {2:.0$}", 5, "x", 0.01);

    // Hello {arg 0 ("x")} is {arg 2 (0.01) with precision specified in arg 1 (5)}
    println!("Hello {0} is {2:.1$}", "x", 5, 0.01);
}
```

将输出：

```
Hello x is 0.01000
Hello x is 0.01000
Hello x is 0.01000
```

这一位还有一个特殊的用法，那就是 `.*`，它不表示一个值，而是表示两个值。第一个值表示精确的位数，第二个值标表示这个值本身。例如：

```rust
fn main() {
    // Hello {next arg ("x")} is {second of next two args (0.01) with precision
    //                          specified in first of next two args (5)}
    println!("Hello {} is {:.*}",    "x", 5, 0.01);

    // Hello {next arg ("x")} is {arg 2 (0.01) with precision
    //                          specified in its predecessor (5)}
    println!("Hello {} is {2:.*}",   "x", 5, 0.01);

    // Hello {next arg ("x")} is {arg "number" (0.01) with precision specified
    //                          in arg "prec" (5)}
    println!("Hello {} is {number:.prec$}", "x", prec = 5, number = 0.01);
}
```

这个例子将输出：

```
Hello x is 0.01000
Hello x is 0.01000
Hello x is 0.01000
```

可以在[标准库文档](https://doc.rust-lang.org/stable/std/fmt/)查看更多 `format!` 的说明。
