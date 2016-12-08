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

这有第二种形式的引用：`&mut T`。一个“可变引用”允许你改变借用的资源。例如：

```rust
let mut x = 5;
{
    let y = &mut x;
    *y += 1;
}
println!("{}", x);
```

这会打印 `6`。我们让 `y` 是一个 `x` 的可变引用，接着把 `y` 指向的值加 1。你会注意到 `x` 也必须被标记为 `mut`，如果它不是，将不能获取一个不可变值的可变引用。

我们在 `y` 前面加了一个星号（`*`），成了 `*y`，这是因为 `y` 是一个 `&mut` 引用，需要使用星号来访问引用的内容。

否则，`&mut` 引用就像一个普通引用。这两者之间，以及他们是如何交互的有巨大区别。你会发现上面的例子有些不太靠谱，因为我们需要额外的作用域，包围在 `{` 和 `}` 之间。如果移除它们，将得到一个错误：

```
   Compiling hello_world v0.1.0 (yourpath/hello_world)
error[E0502]: cannot borrow `x` as immutable because it is also borrowed as mutable
 --> main.rs:7:20
  |
4 |         let y = &mut x;
  |                      - mutable borrow occurs here
...
7 |     println!("{}", x);
  |                    ^ immutable borrow occurs here
8 | }
  | - mutable borrow ends here
```

正如这个例子表现的那样，有一些规则是你必须要掌握的。

#### 规则

Rust 中的借用有一些规则：

第一，任何借用必须位于比拥有者更小的作用域。

第二，对于同一个资源的借用，一下情况不能同时出现在同一个作用域下：

- 拥有 1 个或多个不可变引用（&T）
- 只有一个可变引用（&mut T）

即同一个作用域下，要么只有一个对资源 A 的可变引用（&mut T），要么有 N 个不可变引用（&T），但不能同时存在可变和不可变引用。

这些看起来有点眼熟，虽然并不完全一样，它类似于数据竞争的定义：

>当 2 个或更多的指针同时访问同一内存位置，当它们中至少有 1 个在写，同时操作并不是同步的时候存在一个“数据竞争”

通过引用，你可以拥有你想拥有的任意多的引用，因为它们没有一个在写。如果在写，并且需要 2 个或更多相同内存的指针，则你只能一次拥有一个 `&mut T`。这就是 Rust 如何在编译时避免数据竞争，如果打破规则的话，将得到错误。

#### 作用域

比如这段代码：

```rust
fn main() {
    let mut x = 5;
    
    let y = &mut x;
    *y += 1;
    
    println!("{}", x);
}
```

这段代码会给出如下错误：

```
   Compiling hello_world v0.1.0 (yourpath/hello_world)
error[E0502]: cannot borrow `x` as immutable because it is also borrowed as mutable
 --> main.rs:7:20
  |
4 |     let y = &mut x;
  |                  - mutable borrow occurs here
...
7 |     println!("{}", x);
  |                    ^ immutable borrow occurs here
8 | }
  | - mutable borrow ends here
```

这是因为我们违反了规则：我们有一个指向 `x` 的 `&mut T`，所以不允许创建任何 `&T`。错误信息提示了我们应该如何理解这个错误：

```
8 | }
  | - mutable borrow ends here
```

我们需要的是可变借用在我们尝试调用 `println!` 之前结束并生成一个不可变借用。在 Rust 中，借用绑定在借用有效的作用域上。而我们的作用域看起来像这样：

```
let mut x = 5;

let y = &mut x;    // -+ &mut borrow of x starts here
                   //  |
*y += 1;           //  |
                   //  |
println!("{}", x); // -+ - try to borrow x here
                   // -+ &mut borrow of x ends here
```

这些作用域冲突了：我们不能在 `y` 所在的作用域中同时生成一个 `&x`。

所以我们增加了一个大括号：

```rust
fn main() {
    let mut x = 5;
    {   
        let y = &mut x; // -+ &mut borrow starts here
        *y += 1;        // |
    }                   // -+ ... and ends here
    
    println!("{}", x);  // <- try to borrow x here
}
```

这就没有问题了。我们的可变借用在我们创建一个不可变引用之前离开了作用域。不过作用域是看清一个借用持续多久的关键。

#### 借用规则避免的问题

为什么要有这些限制性规则？这些规则避免了数据竞争。数据竞争能造成何种问题呢？这里有一些。

##### 迭代器失效

当你尝试改变你正在迭代的集合时就会发生迭代器失效的问题。Rust 的借用检查器阻止了这些发生：

```rust
let mut v = vec![1, 2, 3];

for i in &v {
    println!("{}", i);
}
```

这会打印 1 到 3，因为我们在向量上迭代，我们只得到了元素的引用。同时 `v` 本身作为不可变借用，它意味着我们在迭代时不能改变它：

```rust
let mut v = vec![1, 2, 3];

for i in &v {
    println!("{}", i);
    v.push(34);
}
```

这将会错误：

```
   Compiling hello_world v0.1.0 (yourpath/hello_world)
error[E0502]: cannot borrow `v` as mutable because it is also borrowed as immutable
 --> main.rs:6:9
  |
4 |     for i in &v {
  |               - immutable borrow occurs here
5 |         println!("{}", i);
6 |         v.push(34);
  |         ^ mutable borrow occurs here
7 |     }
  |     - immutable borrow ends here
```

我们不能修改 `v`，因为它被循环借用。

##### 释放后使用

引用必须与它引用的值存活得一样长。Rust 会检查你的引用作用域来保证这是正确的。

如果 Rust 并没有检查这个属性，我们可能意外的使用了一个无效的引用。例如：

```rust
let y: &i32;
{
    let x = 5;
    y = &x;
}

println!("{}", y);
```

会得到这个错误：

```
   Compiling hello_world v0.1.0 (yourpath/hello_world)
error: `x` does not live long enough
 --> main.rs:5:14
  |
5 |         y = &x;
  |              ^ does not live long enough
6 |     }
  |     - borrowed value only lives until here
...
9 | }
  | - borrowed value needs to live until here
```

换句话说，`y` 只在 `x` 存在的作用域中有效。一旦 `x` 消失，它变成无效的引用。为此，这的错误说借用“并没有存活得足够久”是因为它应该有效得时候是无效得。

当引用在它引用得变量之前声明会导致类似得问题：

```rust
fn main() {
    let y: &i32;
    let x = 5;
    y = &x;

    println!("{}", y);
}
```

会得到这样得错误：

```
   Compiling hello_world v0.1.0 (yourpath/hello_world)
error: `x` does not live long enough
 --> main.rs:4:10
  |
4 |     y = &x;
  |          ^ does not live long enough
...
7 | }
  | - borrowed value dropped before borrower
  |
  = note: values in a scope are dropped in the opposite order they are created
```

上面得例子中，`y` 在 `x` 之前声明，意味着 `y` 比 `x` 声明周期更长，这是不允许的。

#### 更多例子

这个例子相对前面的复杂点：

```rust
fn main() {
    let mut x: Vec<i32> = vec!(1i32, 2, 3);

    //更新数组
    //push中对数组进行了可变借用，并在push函数退出时销毁这个借用
    x.push(10);

    {
        //可变借用1
        let mut y = &mut x;
        y.push(100);

        //可变借用2，注意：此处是对y的借用，不可再对x进行借用，
        //因为y在此时依然存活。
        let z = &mut y;
        z.push(1000);

        println!("{:?}", z); //打印: [1, 2, 3, 10, 100, 1000]
    } //y和z在此处被销毁，并释放借用。


    //访问x正常
    println!("{:?}", x); //打印: [1, 2, 3, 10, 100, 1000]
}
```

##### 总结

1. 借用不改变内存的所有者（Owner），借用只是对源内存的临时引用。
2. 在借用周期内，借用方可以读这块内存，所有者被禁止读写内存；且所有者保证在有“借用”存在的情况下，不会释放或转移内存。
3. 失去所有权的变量不可以被借用（访问）。
4. 在租借期内，内存所有者保证不会释放/转移/可变租借这块内存，但如果是在非可变租借的情况下，所有者是允许继续非可变租借出去的。
5. 借用周期满后，所有者收回读写权限。
6. 借用周期小于被借用者（所有者）的生命周期。






