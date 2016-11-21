chapter5 原生类型
=================

#### 布尔型：

布尔类型（`bool`）只有两个值：`true` 和 `false`：

```rust
let x = true;
let y: bool = false;
```

布尔型通常用在 `if` 语句中, 也可以用在 `match` 语句中：

```rust
fn main() {
    let praise_the_borrow_checher = true;

    if praise_the_borrow_checher {
        println!("oh, yeah!");
    } else {
        println!("what?!!");
    }

    match praise_the_borrow_checher {
        true => println!("keep praising!"),
        false => println!("you should praise!"),
    }
}
```

还可以将字符串 “true” 和 “false” 转换为 `bool`:

```rust
use std::str::FromStr;

fn main() {
    assert_eq!(FromStr::from_str("true"), Ok(true));
    assert_eq!(FromStr::from_str("false"), Ok(false));
    assert!(<bool as FromStr>::from_str("not even a boolean").is_err());
}
```

也可以这样：

```rust
    assert_eq!("true".parse(), Ok(true));
    assert_eq!("false".parse(), Ok(false));
    assert!("not even a boolean".parse::<bool>().is_err());
```

可以在[标准库文档](https://doc.rust-lang.org/std/primitive.bool.html)查看更多 `bool` 的说明。

#### char

`char` 类型代表一个单独的 Unicode 字符的值。可以使用单引号 `'` 创建 `char`:

```rust
let x = 'x';
let two_hearts = '💕';
```

与其他语言不同的是，Rust 的 `char` 并不是1个字节，而是4个。因而我们可以将各种奇怪的字符随心所欲的赋值给一个 `char` 类型。

可以在[标准库文档](https://doc.rust-lang.org/std/primitive.char.html)查看更多 `char` 的说明。

#### 数字类型

数字类型分为有符号整数（`i8`, `i16`, `i32`, `i64`, `isize`）、无符号整数（`u8`, `u16`, `u32`, `u64`, `usize`）和浮点数（`f32`, `f64`）。其中，`isize` 和 `usize` 也称为可变大小类型或自适应类型，其大小依赖底层机器指针大小，意思就是：32位电脑上就是32位，64位电脑上就是64位。

如果一个数字，没有推断它类型的条件，将采用默认类型：

```rust
let x = 123; //i32
let y = 1.23; //f64;
let c = -456; //i32;
```

需要说明的是，这里的“没有推断它类型的条件” 中的 “条件”。比如：

```rust
fn main() {
    let a = 18446744073709551615;
    println!("{:?}", a);
}
```

Rust 不会根据赋给 `a` 的值推断 `a` 的类型，`a` 仍然是i32类型，把一个大于32位的值赋给i32类型，会警告越界：

```rust
   Compiling hello_world v0.1.0 (yourpath/hello_world)
warning: literal out of range for i32, #[warn(overflowing_literals)] on by default
 --> main.rs:2:13
  |
2 |     let a = 18446744073709551615;
  |             ^^^^^^^^^^^^^^^^^^^^

    Finished debug [unoptimized + debuginfo] target(s) in 0.47 secs
     Running `yourpath\hello_world\target\debug\hello_world.exe`
-1
```

即使能编译成功，也将变为-1。如果给定一个接收者，比如一个函数：

```rust
fn main() {
    let a = 18446744073709551615;
    print(a);
}

fn print(n: u64) {
    println!("{:?}", n);
}
```

这样，Rust 就能根据 `print` 函数所接受的 `u64` 类型推断出 `a` 的类型为 `u64`。

如果显示的标注类型，有两种方式：

```rust
let x: i32 = 123;
let y: f64 = 1.23;
```

或者通过后缀的方式：

```rust
let x = 123i32;
let y = 1.23f64;
```

以上两种方式是等价的。

为了可读性，可以在数字之间加上下划线 `_`：

```rust
let x = 1_000i32; // 1000
let y = 1_234_567; // 1234567
let z = 0.000_001; // 0.000001
```

如果你想的话，这样都可以：

```rust
let x = 1000_i32;
let y = 1.23_f64;
```

还可以加上前缀 `0x`、`0o`、`0b` 分别表示十六进制数、八进制数、二进制数：

```rust
let a = 0b11111111; //255
let b = 0o77777777; //16777215
let c = 0xFFFFFF;  //16777215
```

可以标准库中查看每种类型的详细说明：[i8](https://doc.rust-lang.org/std/primitive.i8.html)，[i16](https://doc.rust-lang.org/std/primitive.i16.html)，[i32](https://doc.rust-lang.org/std/primitive.i32.html)，[i64](https://doc.rust-lang.org/std/primitive.i64.html)，[u8](https://doc.rust-lang.org/std/primitive.u8.html)，[u16](https://doc.rust-lang.org/std/primitive.u16.html)，[u32](https://doc.rust-lang.org/std/primitive.u32.html)，[u64](https://doc.rust-lang.org/std/primitive.u64.html)，[isize](https://doc.rust-lang.org/std/primitive.isize.html)，[usize](https://doc.rust-lang.org/std/primitive.usize.html)，[f32](https://doc.rust-lang.org/std/primitive.f32.html)，[f64](https://doc.rust-lang.org/std/primitive.f64.html)。

#### 数组

未完，待续！

