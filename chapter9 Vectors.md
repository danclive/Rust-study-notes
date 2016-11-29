chapter9 Vectors
================

“Vectors”是一个动态或“可增长”的数组，被实现为标准库类型 [`Vec<T>`](https://doc.rust-lang.org/std/vec/) （其中 `<T>` 是一个泛型语句）。Vectors 是在堆上分配数据。Vectors 与切片就像 `String` 与 `&str` 一样。可以使用 `vec!` 宏创建它：

```rust
let m = vec![1, 2, 3, 4, 5]; // v: vec<i32>
```

对于重复初始值有另一种形式的 `vec!`：

```rust
let m = vec![0; 10]; // [0, 0, 0, 0, 0, 0 ,0 ,0 ,0 ,0]
```

Vectors 将它们的内容以连续的 `T` 的数组的形式存储在堆上，这意味着它们必须在编译时就知道 `T` 的大小（就是存储一个 `T` 需要多少字节）。有些类型的大小不可能在编译时就知道，为此需要保存一个指向该类型的指针，幸好，[`Box`](https://doc.rust-lang.org/std/boxed/) 类型正好适合这种情况。会在后续章节中详细介绍 `Box`。

或者，也可以这样创建一个空的 Vectors，稍后再插入元素：

```rust
let m: Vec<i32> = Vec::new();
let n: Vec<i32> = vec![];
```

类型标注是可以省略的，Rust 可以自动推断出 `T` 的类型：

```rust
fn main() {
    let mut v = vec![];

    v.push(1);
    v.push(2);

    println!("{:?}", v); // v: vec<i32> [1, 2]
}
```

#### 访问元素

可以使用 `[索引]` 访问 Vector 中的值，索引从 `0` 开始，所以第三的元素是 `v[2]`:

```rust
let v = vec![1, 2, 3, 4, 5];

println!("The third element of v is {}", v[2]);
```

另外要注意的是必须用 `usize` 类型的值来索引：

```rust
let v = vec![1, 2, 3, 4, 5];

let i: usize = 0;
let j: i32 = 0;

// works
v[i];

// doesn’t
v[j];
```

#### 越界访问

如果尝试访问并不存在的索引：

```rust
let v = vec![1, 2, 3];
println!("Item 7 is {}", v[7]);
```

当前线程会 `panic` 并输出如下信息：

```
     Running `yourpath\hello_world\target\debug\hello_world.exe`
thread 'main' panicked at 'index out of bounds: the len is 3 but the index is 4', ../src/libcollections\vec.rs:1307
note: Run with `RUST_BACKTRACE=1` for a backtrace.
error: process didn't exit successfully: `yourpath\hello_world\target\debug\hello_world.exe` (exit code: 101)
```

如果想处理越界错误而不是 `panic`，可以使用像 `get` 或者 `get_mut` 这样的方法，当访问一个无效的索引时会返回 `None`：

```rust
let v = vec![1, 2, 3];
match v.get(7) {
    Some(x) => println!("Item 7 is {}", x),
    None => println!("Sorry, this vector is too short.")
}
```

##### 迭代

可以使用 `for` 来迭代 Vector 的元素，有三个版本：

```rust
let mut v = vec![1, 2, 3, 4, 5];

for i in &v {
    println!("A reference to {}", i);
}

for i in &mut v {
    println!("A mutable reference to {}", i);
}

for i in v {
    println!("Take ownership of the vector and its element {}", i);
}
```

要注意的是，不能再使用 Vertor 的所有权遍历它之后再次遍历它，可以使用它的引用多次遍历它。下面的代码将不能编译；

```rust
let v = vec![1, 2, 3, 4, 5];

for i in v {
    println!("Take ownership of the vector and its element {}", i);
}

for i in v {
    println!("Take ownership of the vector and its element {}", i);
}
```

下面的代码没问题：

```rust
let v = vec![1, 2, 3, 4, 5];

for i in &v {
    println!("This is a reference to {}", i);
}

for i in &v {
    println!("This is a reference to {}", i);
}
```

#### 更多用法

Vector 还有更多的用法，比如插入、删除元素等，可以去查看[标准库文档](https://doc.rust-lang.org/std/vec/struct.Vec.html)。



 


