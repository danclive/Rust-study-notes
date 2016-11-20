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

Rust 函数不支持多个返回值，但是我们可以利用元组返回多个值，配合 Rust 的模式匹配，使用起来十分灵活。比如这个例子：

```rust
fn mian() {
  let (p2, p3) = pow_2_3(456);
  println!("pow 2 of 456 is {}.", p2);
  println!("pow 3 of 456 is {}.", p3);
}

fn pow_2_3(n: i32) -> (i32, i32) {
  (n * n, n * n * n)
}
```

这个例子中，`pow_2_3` 函数接收一个 `i32` 类型的值，返回其二次方和三次方的值，这两个数值包装再一个元组中返回。在 `main` 函数中，`let` 语句就可以使用模式匹配将函数返回的元组进行解构，将这两个返回值分别赋给 `p2` 和 `p3`, 从而可以得到 `456` 的二次方和三次方的值。

##### 将函数作为返回值

函数作为返回值，其声明与普通函数的返回类型申明一样：

```rust
fn main() {
   let a = [1,2,3,4,5,6,7];
   let mut b = Vec::<i32>::new();
   for i in &a {
       b.push(get_func(*i)(*i));
   }
   println!("{:?}", b);
}

fn get_func(n: i32) -> fn(i32) -> i32 {
    fn inc(n: i32) -> i32 {
        n + 1
    }
    fn dec(n: i32) -> i32 {
        n - 1
    }
    if n % 2 == 0 {
        inc
    } else {
        dec
    }
}
```

这个例子中，`get_func` 函数接收一个 `i32` 类型的参数，返回一个类型为 `fn(i32) -> i32` 的函数，若传入的参数为偶数，返回 `inc` ，否则返回 `dec`。这里需要注意的是， `inc` 函数和 'dec' 函数的定义在 `get_func` 内，在函数内定义函数在很多其他语言中是不支持的，不过 Rust 支持，这也是 Rust 灵活强大的体现。不过，在函数中定义的函数，不能包含函数中的变量，若要包含，应该使用闭包。所以：

```rust
fn main() {
  let f = get_func();
  println!("{}", f(3));
}

fn get_func() -> fn(i32)->i32 {
  let a = 1;
  fn inc(n:i32) -> i32 {
    n + a
  }
  inc
}
```

这个例子会编译出错：

```rust
   Compiling hello_world v0.1.0 (yourpath/hello_world)
error[E0434]: can't capture dynamic environment in a fn item; use the || { ... } closure form instead
 --> main.rs:9:9
  |
9 |     n + a
  |         ^

error: aborting due to previous error

error: Could not compile `hello_world`.
```

如果改成闭包的话是可以的：

```rust
fn main() {
  let f = get_func();
  println!("{}", f(3));
}

fn get_func() -> Box<Fn(i32) -> i32> {
  let a = 1;
  let inc = move |n| n + a;
  Box::new(inc)
}
```

后续会详细介绍闭包。

##### 发散函数

发散函数是 Rust 中的一个特性。发散函数并不返回，它使用感叹号 `!` 作为返回类型表示。

```rust
fn diverges() -> ! {
    panic!("This function never returns!");
}
```

`panic!()` 是一个宏，类似我们已经见过的 `println!()`。与 `println!()` 不同的是，`panic!()` 导致当前执行的线程崩溃并返回指定的信息。因为这个函数会崩溃，所以它不会返回，所以它拥有一个类型 `!`，它代表“发散”。

如果你添加一个叫做 `diverges()` 的函数并运行:

```rust
fn main() {
  println!("hello");
  diverging();
  println!("world");
}

fn diverging() -> ! {
  panic!("This function will never return");
}
```

由于发散函数不返回，所以就算其后再有其他语句也是不会执行的。如果后面还有其他语句，会出现以下编译警告:

```
   Compiling hello_world v0.1.0 (yourpath/hello_world)
warning: unreachable statement, #[warn(unreachable_code)] on by default
 --> main.rs:4:3
  |
4 |   println!("world");
  |   ^^^^^^^^^^^^^^^^^^
  |
  = note: this error originates in a macro outside of the current crate
```

如果运行这个程序的化:

```
     Running `yourpath\hello_world\target\debug\hello_world.exe`
hello
thread 'main' panicked at 'This function will never return', main.rs:8
note: Run with `RUST_BACKTRACE=1` for a backtrace.
error: process didn't exit successfully: `yourpath\hello_world\target\debug\hello_world.exe` (exit code: 101)
```

发散函数可以被用作任何类型：

```rust
fn diverges() -> ! {
  panic!("This function never returns!");
}
let x: i32 = diverges();
let x: String = diverges();
```

#### 函数指针：

'fn' 关键字可以用来定义函数，除此之外，它还可以用来构造函数类型。与函数定义主要的不同是，构造函数类型不需要函数名、参数名和函数体。

```rust
fn inc(n: i32) -> i32 {
  n + 1
}

type IncType = fn(i32) -> i32;

fn mian() {
  let func: IncType = inc;
  println!("3 + 1 = {}", func(3));
}
```

这个例子中使用 'fn' 定义了 `inc` 函数，它有一个 'i32' 类型的参数，返回 `i32` 类型的值。然后再用 `fn` 定义了一个函数类型，这个函数类型有 `i32` 类型的参数和返回值，并用 `type` 关键字定义了它的别名 `IncType`。在 `main` 函数中定义了一个变量 `func`, 其类型为 `IncType`，并赋值为 `inc` 然后在 `println` 宏中调用：`func(3)`。可以看到，`inc` 函数的类型其实就是 `IncType`。

这里有一个问题，我们将 `inc` 赋值给了 `func` ，而不是 `&inc` ，这样将 `inc` 函数的所有权转移给了 `func` 吗？赋值后还可以以 `inc()` 形式调用 `inc` 函数吗？看这个例子：

```rust
fn main() {
  let func: IncType = inc;
  println!("3 + 1 = {}", func(3));
  println!("3 + 1 = {}", inc(3));
}

type IncType = fn(i32) -> i32;

fn inc(n: i32) -> i32 {
  n + 1
}
```

```
     Running `yourpath\hello_world\target\debug\hello_world`
3 + 1 = 4
3 + 1 = 4
```

这说明，赋值时， `inc` 函数的所有权并没有被转移到 `func` 变量上，而更像不可变引用。在 Rust 中，函数的所有权是不能转移的的，我们给函数类型的变量赋值是，赋给的一般是函数的指针，所以 Rust 中的函数类型，就像是 C/C++ 中的函数指针，当然， Rust 的函数类型更安全。可见，Rust 的函数类型，其实应该属于指针类型（Pointer Type）。Rust 中的指针类型有两种，一种为引用，另一种为原始指针。Rust 中的函数类型是引用类型，因为它是安全的，而原始指针是不安全的，要使用原始指针，必须使用 `unsafe` 关键字申明。

正因为函数类型属于指针类型，所以函数是可以作为数据在程序中进行传递。

```rust
fn one(n: i32) -> i32 {
  n + 1
}

fn two(n: i32) -> i32 {
  n + 2
}

fn three(n: i32) -> i32 {
  n + 3
}

fn main() {
    
    let f1: fn(i32) -> i32 = one;
    let f2: fn(i32) -> i32 = two;
    let f3: fn(i32) -> i32 = three;
    
    let funcs = [f1, f2, f3];
    
    for f in &funcs {
      println!("{:?}", f(1));
    }
}
```

我们将三个类型为 `fn(i32) -> i32` 的函数放到了一个数组中，并且去遍历执行。要注意 `for f in &fnucs` 这里，为什么要用引用呢？如果去掉 `&`：

```rust
   Compiling hello_world v0.1.0 (yourpath/hello_world)
error[E0277]: the trait bound `[fn(i32) -> i32; 3]: std::iter::Iterator` is not satisfied
  --> main.rs:21:5
   |
21 |     for f in funcs {
   |     ^ trait `[fn(i32) -> i32; 3]: std::iter::Iterator` not satisfied
   |
   = note: `[fn(i32) -> i32; 3]` is not an iterator; maybe try calling `.iter()` or a similar method
   = note: required by `std::iter::IntoIterator::into_iter`

error: aborting due to previous error

error: Could not compile `hello_world`.
```

是因为数组没有实现 `std::iter::Iterator` 这个 trait, 而切片（Slices）是一个数组的引用，切片（Slices）是实现了 `std::iter::Iterator` 的。
