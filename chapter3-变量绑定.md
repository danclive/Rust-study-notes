chapter3 变量绑定
=================

变量绑定是指将一些值绑定到一个名字上，这样可以在之后使用他们。Rust 中使用 `let` 声明一个绑定：

```rust
fn main() {
    let x = 123;
}
```

#### 可变性

绑定默认是不可变的（immutable）。下面的代码将不能编译：

```rust
let x = 123;
x = 456;
```

编译器会给出如下错误：

```
   Compiling hello_world v0.1.0 (yourpath/hello_world)
error[E0384]: re-assignment of immutable variable `x`
 --> main.rs:3:5
  |
2 |     let x = 123;
  |         - first assignment to `x`
3 |     x = 456;
  |     ^^^^^^^ re-assignment of immutable variable
```

如果想使得绑定是可变的，使用 mut:

```rust
let mut x = 123;
x = 456;
```

如果你声明了一个可变绑定而又没有改变它，虽然可以编译通过，但编译器还是会给出一些建议和警告：

```rust
let mut x = 123;
```

```
   Compiling hello_world v0.1.0 (yourpath/hello_world)
warning: unused variable: `x`, #[warn(unused_variables)] on by default
 --> main.rs:2:9
  |
2 |     let mut x = 123;
  |         ^^^^^

warning: variable does not need to be mutable, #[warn(unused_mut)] on by default
 --> main.rs:2:9
  |
2 |     let mut x = 123;
  |         ^^^^^

    Finished debug [unoptimized + debuginfo] target(s) in 0.63 secs
```

这正是 Rust 出于安全的目的。

#### 初始化绑定

Rust 变量绑定有另一个不同于其他语言的方面：绑定要求在可以使用它之前必须初始化。

例如以下代码：

```rust
fn main() {
    let x: i32;
    println!("Hello world!");
}
```

Rust 会警告我们从未使用过这个变量绑定，但是因为我们从未用过它，无害不罚。

```
   Compiling hello_world v0.1.0 (yourpath/hello_world)
warning: unused variable: `x`, #[warn(unused_variables)] on by default
 --> main.rs:2:9
  |
2 |     let x: i32;
  |         ^

    Finished debug [unoptimized + debuginfo] target(s) in 0.29 secs
```

然而，如果确实想使用 `x`， 事情就不一样了。修改代码：

```rust
fn main() {
    let x: i32;
    println!("The value of x is: {}", x);
}
```

这是无法通过编译的：

```
   Compiling hello_world v0.1.0 (yourpath/hello_world)
error[E0381]: use of possibly uninitialized variable: `x`
 --> main.rs:3:39
  |
3 |     println!("The value of x is: {}", x);
  |                                       ^ use of possibly uninitialized `x`
<std macros>:2:27: 2:58 note: in this expansion of format_args!
<std macros>:3:1: 3:54 note: in this expansion of print! (defined in <std macros>)
main.rs:3:5: 3:42 note: in this expansion of println! (defined in <std macros>)

error: aborting due to previous error

error: Could not compile `hello_world`.
```

Rust 不允许我们使用一个未经初始化的绑定。

##### 模式

在许多语言中，这叫做变量。不过 Rust 的变量绑定有一些不同的巧妙之处。例如 `let`语句的左侧是一个“模式”，而不仅仅是一个变量。我们也可以这样写：

```rust
let (x, y) = (1, 2);
```

在这个语句被计算后，`x` 将会是1，'y' 将会是2。也许有人会忍不住说：这不类似于 golang 中的多变量绑定嘛。

```go
var x, y = 1, 2
```
事实上不是这样的。`(1, 2)`这种形式是一个元组,是一个固定大小的有序列表。上面的例子还可以写成以下形式：

```rust
let n = (1, "hello");
let (x, y) = n;
```

第一个`let`将一个元组`(1, "hello")`绑定到`n`上，第二个`let`将元组解构，将元组中的字段绑定到`x`和`y`上。

更多模式和元组（Tuple）的特性，将在后续的章节详细介绍。

#### 类型注解

Rust 是一个静态类型语言，意味着我们需要先确定我们需要的类型。事实上，Rust 有一个叫做类型推断的功能，如果它能确认这是什么类型，就不需要明确指定类型。当然也可以显示的标注类型, 类型写在一个冒号':'后面：

```rust
let x: i32 = 123;
```

对于数值类型，也可以这样：

```rust
let x = 123i32; // let x: i32 = 123
let y = 123_i32; // let y: i32 = 123
let z = 100_000i32; // let z: i32 = 100000
```

这样也都没有问题, 后面章节会更详细的介绍。
