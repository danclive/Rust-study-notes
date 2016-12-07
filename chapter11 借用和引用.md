chapter11 借用和引用
====================

#### 借用

在所有权章节的最后，有一个看起来糟糕的例子：

```rust
fn foo(v1: Vec<i32>, v2: Vec<i32>) -> (Vec<i32>, Vec<i32>, i32) {
    // do stuff with v1 and v2

    // hand back ownership, and the result of our function
    (v1, v2, 42)
}

let v1 = vec![1, 2, 3];
let v2 = vec![1, 2, 3];

let (v1, v2, answer) = foo(v1, v2);
```

然而这并不是理想的 Rust 代码，因为它没有利用“借用”这个编程语言的特点。这是它的第一步：

```rust
fn foo(v1: &Vec<i32>, v2: &Vec<i32>) -> i32 {
    // do stuff with v1 and v2

    // return the answer
    42
}

let v1 = vec![1, 2, 3];
let v2 = vec![1, 2, 3];

let answer = foo(&v1, &v2);

// we can use v1 and v2 here!
```

一个更具体的例子：

```rust
fn main() {

    fn sum_vec(v: &Vec<i32>) -> i32 {
        return v.iter().fold(0, |a, &b| a + b);
    }

    fn foo(v1: &Vec<i32>, v2: &Vec<i32>) -> i32 {
        let s1 = sum_vec(v1);
        let s2 = sum_vec(v2);

        s1 + s2
    }

    let v1 = vec![1, 2, 3];
    let v2 = vec![4, 5, 6];

    let answer = foo(&v1, &v2);
    println!("{}", answer);
}
```

与其获取 `Vec<i32>` 作为我们的参数，我们获取一个引用：`&Vec<i32>`。与其直接传递 `v1` 和 `v2`，我们传递 `&v1` 和 `&v2`。我们称 `&T` 类型为一个引用，它并没有拥有这个资源，而是借用了资源的所有权。一个借用变量的绑定在它离开作用域时并不释放资源。这意味着调用 `foo()` 之后，可以再次使用原始的绑定。

引用是不可变的，就像绑定一样。这意味的在 `foo()` 中，向量完全不能被改变：

```rust
fn foo(v: &Vec<i32>) {
     v.push(5);
}

let v = vec![];

foo(&v);
```

会有这样的错误：

```
Compiling hello_world v0.1.0 (yourpath/hello_world)
error: cannot borrow immutable borrowed content `*v` as mutable
 --> main.rs:3:5
  |
3 |     v.push(5);
  |     ^

error: aborting due to previous error

error: Could not compile `hello_world`.
```

#### &mut 引用




