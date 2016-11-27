chapter7 if 语句
================

Rust 的 `if` 并不复杂，它更像动态类型语言而不是传统的系统语言。

`if` 语句是“分支”这个更加宽泛的概念的一个特定形式。它的名字来源于树的树枝：一个选择点，根据选择的不同，将会使用不同的路劲。

在 `if` 语句中，有一个引向两条路径的选择：

```rust
let x = 123;

if x == 123 {
    println!("x is five");
}
```

如果在别的什么地方更改了 `x` 的值，这一行将不会输出。更具体一点，如果 `if` 后面的表达式的值为 `true`，这个代码块将会被执行。为 `false` 则不是执行。

如果想当值为 `false` 时执行些什么，使用 `else`：

```rust
let x = 123;

if x == 123 {
    println!("x is five!");
} else {
    println!("x is not 123 :( !");
}
```

如果不止一种情况，使用 `else if`：

```rust
let x = 123;

if x == 123 {
    println!("x is five!");
} else if x == 456 {
    println!("x is 456!")
} else {
    println!("x is not 123 or 456 :( !");
}
```

这些都是非常标准的情况。然而也可以这么写：

```rust 
let x = 5;

let y = if x == 5 {
    100
} else {
    200
};

println!("y is {}", y); // y is 100
```

或者可以这样：

```rust
let x = 5;

let y = if x == 5 { 100 } else { 200 };

println!("y is {}", y); // y is 100
```

这两段代码都可以执行，是因为 `if` 是一个表达式。表达式的值是人格被选择的分支的最后一个表达式的值。一个没有 `else` 的 `if` 总是返回 `()` 作为返回值。

但是要注意以下的代码会报错：

```rust
let x = 5;

let y = if x == 5 {
    100;
} else {
    200;
};

println!("y is {}", y);
```

因为，表达式如果返回，总是返回一个值，但是语句不返回值或者返回 `()`。

