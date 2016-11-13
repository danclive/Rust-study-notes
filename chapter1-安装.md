chapter1 安装
=============

#### 在 Linux 和 Mac 上安装

如果使用 Linux 或 Mac, 最简单的做法就是打开一个终端并输入:

```
$ curl -sSf https://static.rust-lang.org/rustup.sh | sh
```

这将会下载一个脚本，并开始安装。如果一切顺利，你将会看到：

```
Rust is ready to roll.
```

然后输入 Y，并按照接下来的提示操作。  

由于网络的原因，可能会出现下载不了或者下载中断的情况，可能需要代理，或者可以尝试从官网[下载](https://www.rust-lang.org/zh-CN/downloads.html)对应操作系统的安装包。注意操作系统版本(64位/32位)，Linux 为 .tar.gz 压缩包，解压并运行里面的 install.sh; Mac 为 .pkg 包，可直接安装。

如果想要卸载的话，在 Linux 和 Mac 上可以运行卸载脚本

```
$ sudo /usr/local/lib/rustlib/uninstall.sh
```

#### 在 Windows 上安装

如果使用 Windows 系统，需要[下载](https://www.rust-lang.org/zh-CN/downloads.html)相应的安装包（.msi）。目前有基于 GNU 和 基于 MSVC 构建的两个版本，推荐安装基于 GNU 的版本。

如果想要卸载，可以通过控制面板或者再次运行安装包。

#### 验证

安装了 Rust 后，可以打开一个终端并输入：

```
$ rustc --version
```

这时候如果输出版本号、提交的 hash 值和提交时间，例如：

```
rustc 1.12.1 (d4f39402a 2016-10-19)
```

那么 Rust 已经安装成功了。  

如果在 Windows 系统下，可能需要将 Rust 添加到 PATH 系统变量中。

#### Hello, world!

现在已经安装好了 Rust，接下来编写第一个程序。  

首先选择合适的位置，建议是在你的 home 目录下，创建一个目录用来存放 Rust 源码：

```
$ mkdir hello_world
$ cd hello_world
```

然后使用喜欢的编辑器，创建一个叫做 `main.rs` 的源文件。Rust 代码文件使用 `.rs` 作为后缀，如果文件名由多个单词组成，使用下划线分割它们，例如`hello_world.rs`。在刚刚创建的`main.rs` 文件，输入如下代码：

```
fn main() {
    println!("Hello, world!");
}
```

保存文件，并回到命令行窗口。输入：

```
$ rustc main.rs
```

这时候，已经编译生成可执行文件了。可以直接运行 main 或 main.exe, 应该可以输出 `Hello world!`，恭喜！

也可以在编译时添加参数`-O`:

```
$ rustc main.rs -O
```

这时候将进行优化编译，编译出来的程序会更小，运行速度也会快一点。

