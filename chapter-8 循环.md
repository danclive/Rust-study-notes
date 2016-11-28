chapter-8 循环.md
=================

#### for

`for` 用来循环一个特定的次数。不过，Rust 的 `for` 循环与其他语言有些不同，看起来并不像这个“ C 语言样式”的 `for` 循环：

```c
for (x = 0; x < 10; x++) {
    printf("%d\n", x);
}
```

相反，它看起来像这个样子：

```rust
for x in 0..10 {
    println!{"{}", x};
}
```

更抽象的形式：

```rust
for var in iterator {
    code
}
```

这个表达式是一个迭代器，迭代器返回一系列的元素。每个元素是循环中的一次重复。然后它的值与 `var` 绑定，在它的循环体中有效。每当循环体执行完后，我们从迭代器中取出下一个值，然后再重复一遍。当迭代器中不在有值时，`for` 循环结束。

在上面那个例子中，`0..10` 表达式取一个开始和结束的位置，然后给出一个含有这之间值的迭代器。当然它不包括上限值，所以这个循环会打印 `0` 到 `9`，而不是 `10`：

```
0
1
2
3
4
5
6
7
8
9
```

Rust 没有使用“C语言风格”的 `for` 循环是有意为之的。即使对于有经验的 C 语言开发者来说，要手动控制循环的每个元素也都是复杂而且容易出错的。

后续章节会详细介绍迭代器。

##### Enmuerate 方法

但你需要记录已经循环了多少次的时候，可以使用 `enumerate()` 函数。

```rust
for (i, j) in (5..0).enumerate() {
    println!("i = {} and j = {}", i, j);
}
```

输出：

```
i = 0 and j = 5
i = 1 and j = 6
i = 2 and j = 7
i = 3 and j = 8
i = 4 and j = 9
```

要记得在范围外面加上括号。

这样也可以：

```rust
let lines = "hello\nworld".lines();

for (linenumber, line) in lines.enumerate() {
    println!("{}: {}", linenumber, line);
}
```

输出：

```
0: hello
1: world
```

#### loop

`loop` 是 Rust 提供的最简单的循环了，用来创建一个无限循环，看起来像这样：

```rust
loop {
    println!("Loop forever!");
}
```

#### while

Rust 中也有一个 `while` 循环。看起来像这样：

```rust
let mut x = 5; // mut x: i32
let mut done = false; // mut done: bool

while !done {
    x += x - 3;

    println!("{}", x);

    if x % 5 == 0 {
        done = true;
    }
}
```

`while` 循环是当你不确定应该循环多少次时正确的选择。

`while` 也可以创建无限循环：

```rust
while true {

}
```

不过，但你打算无限循环的时候应该倾向于使用 `loop`。

#### 提早结束循环（breakh  和 continue）

再看之前的  `while` 循环：

```rust
let mut x = 5;
let mut done = false;

while !done {
    x += x - 3;

    println!("{}", x);

    if x % 5 == 0 {
        done = true;
    }
}
```

我们必须使用一个 `mut` 布尔型变量绑定，`done` 来确定何时我们应该退出循环。

不过，我们可以用 `break` 来写一个更好的循环：

```rust
let mut x = 5;

loop {
    x += x - 3;

    println!("{}", x);

    if x % 5 == 0 { break; }
}
```

现在我们用 `loop` 来无限循环，然后用 `break` 来提前退出循环。

`continue` 比较类似，不过不是退出循环，它直接进行下一次迭代。下面的例子只会打印奇数：

```rust
for x in 0..10 {
    if x % 2 == 0 { continue; }

    println!("{}", x);
}
```

`break` 和 `continue` 在 `while` 循环和 `for` 循环中同样有效。

#### 循环标签（Loop labels)

也许你会遇到这样的情形，当你有嵌套的循环而希望指定你的哪一个 `break` 和 `continue` 该起作用。就像大多数语言，默认 `break` 或 `continue` 将会作用于最内层的循环。当你想要一个 `break` 或 `continue` 作用于于一个外层循环，你可以使用标签来指定 `break` 或 `continue` 语句作用的循环。下面的代码只会在 `x` 和 `y` 都为奇数时打印它们：

```rust
'outer: for x in 0..10 {
    'inner: for y in 0..10 {
        if x % 2 == 0 { continue 'outer; } // continues the loop over x
        if y % 2 == 0 { continue 'inner; } // continues the loop over y
        println!("x: {}, y: {}", x, y);
    }
}
```

