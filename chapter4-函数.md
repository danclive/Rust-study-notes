chapter4 函数
=============
Rust 是一门多范式的编程语言，但 Rust 的编程风格是更偏向于函数式的，函数在 Rust 中是“一等公民”。这意味着，函数是可以作为数据在程序中进行传递。

跟 C/C++/Go 一样，Rust 程序也有一个唯一的程序入口 `main` 函数,之前在 hello world 例子中已经见到过了：

```rust
fn main() {

}
```

Rust 使用 `fn` 关键字来声明和定义函数，后面跟着函数名，一对括号是因为这函数没有参数，然后是一对大括号代表函数体。下面是一个叫做 `foo` 的函数：

```rust
fn foo() {

}
```

如果函数有返回值，在括号后面加上箭头 `->` ，接着是返回类型：

```
fn main() {
    println!("{:?}", add(1, 2));
}

fn add(x: i32, y: i32) -> i32 {
    x + y
}
```

`add` 函数可以将两个数值加起来并将结果返回。

#### 函数参数

##### 参数声明

Rust 的函数参数声明和一般的变量声明非常相似：参数名加上冒号再加上参数类型，不过不需要 `let` 关键字。例如这个程序将两个数相加并打印结果：

```rust
fn main() {
    print_sum(5, 6);
}

fn print_sum(x: i32, y: i32) {
    println!("sum is: {}", x + y);
}
```

在调用函数和声明函数时，多个参数需要用逗号 `,` 分隔。与 `let` 不同的是，普通变量声明可以省略变量类型，而函数参数声明则不能省略参数类型。下面的代码不能够工作：

```rust
fn main() {
    print_sum(5, 6);
}

fn print_sum(x, y) {
    println!("sum is: {}", x + y);
}
```

编译器将会给出以下错误：

```
   Compiling hello_world v0.1.0 (yourpath/hello_world)
error: expected one of `:` or `@`, found `,`
 --> main.rs:5:15
  |
5 | fn print_sum(x, y) {
  |               ^

error: expected one of `:` or `@`, found `)`
 --> main.rs:5:18
  |
5 | fn print_sum(x, y) {
  |                  ^

error[E0425]: unresolved name `x`
 --> main.rs:6:28
  |
6 |     println!("sum is: {}", x + y);
  |                            ^
<std macros>:2:27: 2:58 note: in this expansion of format_args!
<std macros>:3:1: 3:54 note: in this expansion of print! (defined in <std macros>)
main.rs:6:5: 6:35 note: in this expansion of println! (defined in <std macros>)

error[E0425]: unresolved name `y`
 --> main.rs:6:32
  |
6 |     println!("sum is: {}", x + y);
  |                                ^
<std macros>:2:27: 2:58 note: in this expansion of format_args!
<std macros>:3:1: 3:54 note: in this expansion of print! (defined in <std macros>)
main.rs:6:5: 6:35 note: in this expansion of println! (defined in <std macros>)

error[E0061]: this function takes 0 parameters but 2 parameters were supplied
 --> main.rs:2:5
  |
2 |     print_sum(5, 6);
  |     ^^^^^^^^^^^^^^^ expected 0 parameters

error: aborting due to previous error

error: Could not compile `hello_world`.
```

这是一个有意而为之的设计决定。即使像 Haskkell 这样的能够全程序推断的语言，注明类型也经常作为一个最佳实践被建议。不过，还有一种特殊的函数,闭包（closure, lambda），类型标注是可选的。将在后续章节中介绍闭包。

##### 将函数作为参数

在 Rust 中，函数是一等公民（可以存储在变量/数据结构、可以作为参数传入函数、可以作为返回值），所以函数参数不仅可以是一般类型，也可以是函数。如：

```rust
fn main() {
  let xm = "xiaoming";
  let xh = "xiaohong";
  say_what(xm, hi);
  say_what(xh, hello);
}

fn hi(name: &str) {
  println!("Hi, {}.", name);
}

fn hello(name: &str) {
  println!("Hello, {}.", name);
}

fn say_what(name: &str, func: fn(&str)) {
  func(name)
}
```

这个例子中，`hi` 函数和 `hello` 函数都只有一个 `&str` 类型的参数而且没有返回值。`say_what` 函数有两个参数，一个是 `&str` 类型，另一个是函数类型（function type），它只有一个 `&str` 类型的参数且没有返回值的行数类型。

##### 模式匹配

又一次提到了模式，模式匹配给 Rust 增添了许多灵活性。模式匹配不仅可以用在变量申明中，也可以用在函数参数申明中，如：

```rust
fn main() {
  let xm = ("xiaoming", 54);
  let xh = ("xiaohong", 66);
  print_id(xm);
  print_id(xh);
  print_name(xm);
  print_age(xh);
  print_name(xm);
  print_age(xh);
}

fn print_id((name, age): (&str, i32)) {
  println!("I'm {},age {}.", name, age);
}

fn print_age((_, age): (&str, i32)) {
  println!("My age is  {}", age);
}

fn print_name((name,_): (&str, i32)) {
  println!("I am  {}", name);
}
```

这是一个元组（Tuple）匹配的例子，当然也可以是其他可以在 `let` 语句中使用的类型。参数的匹配跟 `let` 语句的匹配一样，也可以使用下划线表示丢弃一个值。

#### 函数返回值

在 Rust 中，任何函数都有放回类型，当函数返回时，会返回一个该类型的值。再来看看 `main` 函数：

```rust
fn main() {

}
```

函数的返回值类型是在参数列表后，加上箭头 `->` 和类型来指定的。不过，一般我们看到的 `main` 函数的定义并没有这么做。这是因为 'main' 函数的返回值是 `()`, 在 Rust 中，当一个函数返回 `()` 时，可以省略。`main` 函数的完整形式如下：

```rust
fn main() -> () {

}
```

`main` 函数的返回值类型是 `()`, 它是一个特殊的元组——一个没有元素的元组，称之为 `unit`，它表示一个函数没有任何信息需要返回。`()` 类型，类似于 C/C++、Java、C# 中的 `void` 类型。

下面看一个有返回值的例子：

```rust
fn main() {
  let a = 123;
  println!("{}", inc(a));
}

fn inc(n: i32) -> i32 {
  n + 1
}
```

这个例子中，`inc` 函数有一个 `i32` 类型的参数和返回值，作用是将参数加 1 返回。需要注意的是 `inc` 函数中只有一个 `n + 1` 一个表达式，并没有像 C/C++ 等语言有显示的 `return` 语句返回一个值。这是因为，与其他语言不同，Rust 是基于表达式的语言，函数中最后一个表达式的值，默认作为返回值（注意：没有分号 `:`）。稍后会介绍语句和表达式。

##### return 关键字

Rust 也有 `return` 关键字，不过一般用于提早返回。看这个例子：

```rust
fn main() {
  let a = [1, 2, 3, 4, 8, 9];
  println!("There is 7 in the array: {}", find(7, &a));
  println!("There is 8 in the array: {}", find(8, &a));
}

fn find(n: i32, a: &[i32]) -> bool {
  for i in a {
    if *i == n {
      return true;
    }
  }

  false
}
```

`find` 函数接受一个 `i32` 类型 `n` 和一个 `i32` 类型的切片(`slice`)`a` ，返回一个 `bool` 值，若 `n` 是 `a` 的元素，则返回 'true'，否则返回 `false`。可以看到，`return` 关键字，用在 `for` 循环 `if` 表达式中，若此时 `a` 的元素与 `n` 相等，则立刻返回 `true`，剩下的循环不必再进行，否则一直循环检测完整个切片(`slice`)，最后返回 `false`。

不过，`return` 语句也可以用在最后返回，把 `find` 函数最后一句 `false` 改为 `return false;` （注意分号不可以省略）也是可以的：

```rust
fn main() {
  let a = [1, 2, 3, 4, 8, 9];
  println!("There is 7 in the array: {}", find(7, &a));
  println!("There is 8 in the array: {}", find(8, &a));
}

fn find(n: i32, a: &[i32]) -> bool {
  for i in a {
    if *i == n {
      return true;
    }
  }

  return false;
}
```

不过这是一个糟糕的风格，不是 Rust 的编程风格了。

需要注意的是，`for` 循环中的 `i` ，其类型为 `&i32`，需要使用解引用来变换为 `i32` 类型。切片（`slice`）可以看作是对数组的引用，后面的章节中会详细介绍切片（`slice`）。

##### 返回多个值

未完，待续~