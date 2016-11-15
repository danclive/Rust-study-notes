chapter2 使用 Cargo
===================

Cargo 是 Rust 的构建系统和包管理工具，Cargo 负责三个工作：构建你的代码，下载你代码依赖的库并便利这些库。最简单的 Rust 程序并没有任何依赖，但随着编写的程序更加复杂而不得不引入其他库，如果一开始就使用 Cargo的话，这将会简单许多。

如果是使用官方安装包的话，Rust 已经自带了 Cargo，也可以打开一个终端，输入：

```
$ cargo
```

如果可以看到 Usage 等信息，那一切 OK！

#### Hello world！

现在使用 Cargo 编写 Hello world 程序。打开一个终端，进入一个合适的目录，输入：

```
$ cargo new hello_world --bin
```

然后，可以看到：

```
    Created binary (application) `hello_world` project
```

此时，已经在当前目录下创建了一个基于 Cargo 管理的项目，名为 hello_world。`--bin` 表示该项目将生成一个可执行文件。具体生成的项目目录结构如下：

```
.
├── Cargo.toml
└── src
    └── main.rs
```

打开 main.rs 文件可以看到，cargo new 命令已经生成好了 hello_world 运行必须的代码：

```
fn main() {
    println!("Hello, world!");
}
```

然后进入到这个项目目录，输入：

```
$ cargo build
```

稍等片刻：

```
   Compiling hello_world v0.1.0 (file:/youpath/hello_world)
    Finished debug [unoptimized + debuginfo] target(s) in 0.91 secs
```

那说明程序已经编译好了。接着：

```
$ cargo run
    Finished debug [unoptimized + debuginfo] target(s) in 0.0 secs
     Running `target\debug\hello_world`
Hello, world!
```

OK！hello_world 已经运行并输出了。其实，这里也可以不用执行 `cargo build` 而直接 `cargo run`, rust 会为我们编译并运行程序：

```
& cargo run
   Compiling hello_world v0.1.0 (file:/yourpath/hello_world)
    Finished debug [unoptimized + debuginfo] target(s) in 0.35 secs
     Running `target\debug\hello_world.exe`
Hello, world!
```

#### 基于 Cargo 的 Rust 项目组织结构

这是一个 基于 Cargo 的项目的结构图：

```
.
├── Cargo.toml
├── Cargo.lock
├── tests
├── examples
├── benches
├── target
    ├── debug
    └── release
└── src
    ├── lib.rs
    └── main.rs
```

Cargo.toml 和 Cargo.lock 位于项目的根目录下，是 Cargo 代码管理的核心，Cargo 工具的所有活动均基于这两个文件。

外部测试源码文件位于 tests 目录下。

示例程序源码文件位于 examples 目录下。

基准测试源码文件位于 benches 目录下。

target 目录下 debug 和 release 目录用于存放编译生成的中间文件和最终的可执行文件。

#### Cargo.toml 和 Cargo.lock

未完！待续
