---
title: Golang常用命令
tags:
  - Go
categories: Go
abbrlink: 62614
date: 2019-09-12 09:51:44
---

## 命令汇总

在终端输入 `go help` 可显示所有的命令及功能简介，比较常用的有：

- `go build` 用于编译我们指定的源码文件或代码包以及它们的依赖包
- `go get` 根据要求和实际情况从互联网上下载或更新指定的代码包及其依赖包，并对它们进行编译和安装。
- `go test` 用于对Go语言编写的程序进行测试
- `go clean` 删除掉执行其它命令时产生的一些文件和目录
- `go env` 用于打印Go语言的环境信息

每个命令的参数可以用 `go help <command>` 查看

```shell
Go is a tool for managing Go source code.

Usage:

	go <command> [arguments]

The commands are:

	bug         start a bug report
	build       compile packages and dependencies
	clean       remove object files and cached files
	doc         show documentation for package or symbol
	env         print Go environment information
	fix         update packages to use new APIs
	fmt         gofmt (reformat) package sources
	generate    generate Go files by processing source
	get         download and install packages and dependencies
	install     compile and install packages and dependencies
	list        list packages or modules
	mod         module maintenance
	run         compile and run Go program
	test        test packages
	tool        run specified go tool
	version     print Go version
	vet         report likely mistakes in packages

Use "go help <command>" for more information about a command.

Additional help topics:

	buildmode   build modes
	c           calling between Go and C
	cache       build and test caching
	environment environment variables
	filetype    file types
	go.mod      the go.mod file
	gopath      GOPATH environment variable
	gopath-get  legacy GOPATH go get
	goproxy     module proxy protocol
	importpath  import path syntax
	modules     modules, module versions, and more
	module-get  module-aware go get
	module-auth module authentication using go.sum
	module-private module configuration for non-public modules
	packages    package lists and patterns
	testflag    testing flags
	testfunc    testing functions

Use "go help <topic>" for more information about that topic.
```

## 参考

- [Go 命令教程-极客学院](http://wiki.jikexueyuan.com/project/go-command-tutorial/0.1.html)
- [About the go command](https://golang.org/doc/articles/go_command.html)
- [Command go](https://golang.org/cmd/go/)

---
