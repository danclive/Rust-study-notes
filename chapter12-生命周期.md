chapter12 生命周期
==================

#### 生命周期

借出一个其他人所有资源的引用可以是很复杂的。例如以下操作：

- 我获取了一个某种资源的句柄
- 我借给你了一个关于这个资源的引用
- 我决定不再需要这个资源了，然后释放了它，这时你仍然持有它的引用
- 你决定使用这个资源

你的引用指向一个无效的资源。这叫做悬垂指针，如果这个资源是内存的话。

如果要修正这个问题，我们必须确保第四步永远也不在第三步之后发生。Rust 所有权系统通过一个叫生命周期的概念来做到这一点，它定义了一个引用有效的作用域。

当我们有一个获取引用作为参数的函数，我们可以隐式或显式涉及到引用的生命周期：

```rust
// implicit
fn foo(x: &i32) {
}

// explicit
fn bar<'a>(x: &'a i32) {
}
```

`'a` 读作“生命周期 a”。技术上讲，每一个引用都有着一些与之相关的生命周期，不过通常情况下编译器可以让你省略它们。这是显式的：

```rust
fn bar<'a>(...)
```

之前在讨论函数的时候并没有讨论函数名后面的 `<>`。一个函数可以在 `<>` 之间有“泛型参数”，生命周期也是其中一种。后续章节会介绍其他类型的泛型，现在先来看生命周期。

我们用 `<>` 声明了生命周期。这是说 `bar` 有一个生命周期 `'a`。如果我们有两个引用参数，它看起来应该是这样：

```rust
fn bar<'a, 'b>(...)
```

接着在我们的参数列表中，我们使用了我们命名的生命周期：

```rust
...(x: &'a i32)
```

如果想要一个 `&mut` 引用，可以这么做：

```rust
...(x: &'a mut i32)
```

如果对比一下 `&mut i32` 和 `&'a mut i32`，它们是一样的，只是后者在 `&` 和 `mut i32` 之间夹了一个生命周期 `'a`。`&mut i32` 读作“一个 `i32` 的可变引用”，而 `&'a mut i32` 读作“一个带有生命周期 `'a` 的 `i32` 的可变引用。

#### 在 `struct` 中

当处理结构体时也需要显式的生命周期：

```rust
struct Foo<'a> {
    x: &'a i32,
}

fn main() {
    let y = &5;
    let f = Foo { x: y };

    println!("{}", f.x);
}
```

如你所见， `struct` 也可以有生命周期。

然而为什么这里需要一个生命周期呢？因为我们需要确保任何 `Foo` 的引用不能比它包含的 `i32` 的引用活的更久。

#### `impl` 块

我们在 `Foo` 中实现一个方法：

```rust
struct Foo<'a> {
    x: &'a i32,
}

impl<'a> Foo<'a> {
    fn x(&self) -> &'a i32 {
        self.x
    }
}

fn main() {
    let y = &5;
    let f = Foo{ x: y };

    println!("x is: {}", f.x());
}
```

如你所见，我们需要在 `impl` 行为 `Foo` 声明一个生命周期。我们重复了两次 `'a`，就像在函数中：`impl<'a>` 定义了一个生命周期 `'a`，而 `Foo<'a>` 使用它。

#### 多个生命周期

如果你有多个引用，可以多次使用同一个生命周期：

```rust
fn x_or_y<'a>(x: &'a str, y: &'a str) -> &'a str {
    x
}
```

这意味着 `x` 和 `y` 存活在同样的作用域内，并且返回的值也同样存活在这个作用域内。如果想要 `x` 和 `y` 有不同的生命周期，可以使用多个生命周期参数：

```rust
fn x_or_y<'a, 'b>(x: &'a str, y: &'b str) -> &'a str {
    x
}
```

在这个例子中，`x` 和 `y` 有不同的有效作用域，不过返回值和 `x` 有相同的生命周期。

#### 理解作用域

理解生命周期的一个办法是想象一个引用有效的作用域。例如：

```rust
fn mian() {
    let y = &5;    // -+ y goes into scope
                   //  |
    // stuff       //  |
                   //  |
}                  // -+ y goes out of scope
```

加入 `Foo`：

```rust
struct Foo<'a> {
    x: &'a i32,
}

fn main() {
    let y = &5;             // -+ y goes into scope
    let f = Foo { x: y };   // -+ f goes into scope
    // stuff                //  |
                            //  |
}                           // -+ f and y go out of scope
```

`f` 生存在 `y` 的作用域之中，所以一切正常。那么如果不是呢？下面的代码不能工作：

```rust
struct Foo<'a> {
    x: &'a i32,
}

fn main() {
    let x;                      // -+ x goes into scopr
                                //  | 
    {                           //  |
        let y = &5;             // ---+ y goes into scope
        let f = Foo { x: y };   // ---+ f goes into scope
        x = &f.x;               //  | erroe here
    }                           // ---+ f and y go out of scope
                                //  |
    println!{"{}", x};          //  |
}                               // -+ x goes out of scope
```

就像你在这里看到的一样，`f` 和 `y` 的作用域小于 `x` 的作用域。不过当我们尝试 `x = &f.x` 时，让 `x` 引用一些将要离开作用域的变量。

命名作用域用来赋予作用域一个名字。有了名字时我们可以谈论它的第一步。

#### `'static`

叫做 `static` 的作用域时特殊的。它代表具有横跨整个程序的生命周期。比如：

```rust
let x: &'static str = "Hello, world.";
```

基本字符串是 `&'static str` 类型，是因为它的引用一直有效：它们被写入了最终库文件的数据段。另一个例子是全局量：

```rust
static FOO: i32 = 5;
let x: &'static i32 = &FOO;
```

它在二进制文件的数据段中保存了一个 `i32`，而 `x` 是它的一个引用。

#### 生命周期沈略

Rust 支持强大的在函数体中的局部类型推断，不过在这项签名中是禁止的以便允许只通过项签名本身推导出类型。然而，出于人体工程学方面的考虑，有第二个非限制的叫做“生命周期省略”的推断算法适用于函数签名。它只基于签名部分自身推断而不涉及函数体，只推断生命周期参数，并且只基于3个易于记忆和无歧义的规则，虽然并不隐藏它涉及到的实际类型，因为局部推断可能会适用于它。

当我们讨论生命周期省略的时候，我们使用输入生命周期和输出生命周期（input lifetime and out oifetime）。输入生命周期是关于函数参数的，而输出生命周期是关于函数返回值的。例如，这个函数有一个输入生命周期：

```rust
fn foo<'a>(bar: &'a str)
```

这个有一个输出生命周期：

```rust
fn foo<'a>() -> &'a str
```

这个两者皆有：

```rust
fn foo<'a>(bar: &'a str) -> &'a str
```

有3条规则：

- 每一个被省略的函数参数成为一个不同的生命周期参数。
- 如果刚好有一个输入生命周期，不管是否省略，这个生命周期被赋予所有函数返回值中被省略的生命周期。
- 如果有多个输入生命周期，不管它们当中有一个是 `&self` 或者 `&mut self`，`self` 的生命周期被赋予多有省略的输出生命周期。

否则，省略一个输出生命周期将是一个错误。

##### 例子

这里有一些省略了生命周期的函数的例子。我们用它们的扩展形式配对了每个省略生命周期的例子。

```rust

fn print(s: &str);
fn print<'a>(s: &'a str);

fn debug(lvl: u32, s: &str);
fn debug(lvl: u32, s: &'a str);

// In the preceding example, 'lvl' doesn't need a lifetime because it's not
// a reference ('&'). Only things relating to referenses (such as a 'struct'
// which contains a reference) need lifetimes.

fn substr(s: &str, until: u32) -> &str;
fn substr<'a>(s: &'a str, until: u32) -> &'a str;

fn get_str() -> &str; // 非法，没有输入值

fn frob(s: &str, t: &str) -> &str; // 非法，两个输入值
fn frob<'a, 'b>(s: &'a str, t: &'b str) -> &str; // 输出生命周期不明确

fn get_mut(&mut self) -> &mut T;
fn get_mut<'a>(&'a mut self) -> &'a mut T;

fn args<T: ToCStr>(&mut self, args: &[T]) -> &mut Command;
fn args<'a, 'b, T: ToCStr>(&'a mut self, args: &'b [T]) -> &'a mut Command;

fn new(buf: &mut [u8]) -> BufWriter;
fn new<'a>(buf: &'a mut [u8]) -> BufWriter<'a>;
```

#### 生命周期推导

要推导生命周期是否合法，先明确两点：

- 输出值（返回值）依赖哪些输入值
- 输入值的生命周期大于或等于输出值的生命周期（准确来说，子集，而不是大于或等于）

生命周期推导公式：当输出值 R 依赖输入值 X, Y, Z...,当且仅当输出值的生命周期为所有输入值的生命周期的子集时，生命周期合法。

```
Lifetime(R) ⊆ ( Lifetime(X) ∩ Lifetime(Y) ∩ Lifetime(Z) ∩ Lifetime(...) )
```

比如：

```rust
fn foo<'a>(x: &'a str, y: &'a str) -> &'a str {
    if true {
        x
    } else {
        y
    }
}
```

因为返回值同时依赖输入参数 `x` 和 `y`, 所以：

```
Lifetime(返回值) ⊆ ( Lifetime(x) ∩ Lifetime(y) )

即：

'a ⊆ ('a ∩ 'a)  // 成立
```

再比如：

```rust
fn foo<'a, 'b>(x: &'a str, y: &'b str) -> &'a str {
    if true {
        x
    } else {
        y
    }
}
```

这段代码会编译错误，编译器无法推导返回值的生命周期。

因为返回值同时依赖 `x` 和 `y`，所以：

```
Lifetime(返回值) ⊆ ( Lifetime(x) ∩ Lifetime(y) )

即：

'a ⊆ ('a ∩ 'b)  //不成立
```

这种情况下，可以显式地告诉编译器 `'b` 比 `'a` 长（`'a` 是 `'b` 的子集），只需要定义生命周期的时候，在 `'b` 的后面加上 `: 'a`，意思是 `'b` 比 `'a` 长，`'a` 是 `'b` 的子集：

```rust
fn foo<'a, 'b: 'a>(x: &'a str, y: &'b str) -> &'a str {
    if true {
        x
    } else {
        y
    }
}
```

这样就可以编译通过，因为：

```
条件：Lifetime(x) ⊆ Lifetime(y)
推导：Lifetime(返回值) ⊆ ( Lifetime(x) ∩ Lifetime(y) )

即：

条件： 'a ⊆ 'b
推导：'a ⊆ ('a ∩ 'b) // 成立
```


