---
title: MaxOSX安装Rust 及 入门
hugo.Data: 2018-06-24
date: 2018-06-24
draft: false
tags:
categories:
  - rust
---

## 安装
### 第一步, 安装`rustup`
打开终端, 输入命令:

	curl https://sh.rustup.rs -sSf | sh

Tip: 

1. `brew install rust`感觉有点问题, 还是用官方推荐的命令比较好.
2. 安装有点慢, 第一步下载`setup-init`时, 可以给`curl`设置代理; 到了第二步, `setup-init`会使用内部的`curl`下载, 暂时没有找到设置代理的方法.

### 第二步, 安装`rust`及套件(docs, std, cargo, rustc)
终端继续输入命令:

	rustup install nightly
	rustup default nightly
	
这样就算安装完成了.

TIP:

- 更新`rust`命令: `rustup update`
- 更新`rustup`命令: `rustup self update`

简略说明:

- rustc: `rust`编译器, `rs`文件编译成二进制可执行文件
- rust-docs: rust的文档
- rust-std: rust的标准库
- cargo: rust项目构建工具

## 基本使用
### rustc(一般都不用)
	rustc main.rs
	./main

### cargo
创建项目

	cargo new <project_path> [--bin|lib] [--name]

- `--bin`是构建二进制可执行文件工程(默认), `--lib`是构建库
- `--name`指定工程名, 默认为工程目录名

项目结构

	src(源码目录)
		- main.rs 
	Cargo.toml(项目配置文件)
	
构建工程(终端当前路径为项目根目录)

	cargo build # 构建工程
	cargo run # 运行工程, 一般用这个就好, 源码修改后, 会自动构建
	
## Hello World
`main.rs`
	
	fn main() {
		println!("Hello World");
	}
	
- `fn`定义函数
- `println!`是一个宏, 打印字符串输出

## 参考
- <https://www.rust-lang.org/zh-CN/install.html>
- <https://github.com/rust-lang-nursery/rustup.rs/blob/master/README.md>