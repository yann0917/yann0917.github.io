---
title: Go生成固定长度随机字符串
date: 2019-08-30 13:55:37
tags:
 - Go
categories: Go
---

## 知识点

1. 编码
   1. golang 默认编码是 `utf-8`;
   2. golang 中 `string` 底层是通过 `byte数组` 实现的;
   3. 中文字符在unicode下占2个字节，在utf-8编码下占3个字节;
2. 基础数据类型
   1. `rune` 数据类型是 `int32` 的别名，常用来处理unicode或utf-8字符，例如汉字的长度使用 `len([]rune("你好"))`;
   2. `byte` 数据类型是 `uint8` 的别名，常用来处理ascii字符
3. `rand` 包实现了用于加解密的更安全的随机数生成器
   1. `rand.Seed(time.Now().UnixNano())` 设置随机种子
   2. `rand.NewSource(time.Now().UnixNano())`
   3. `Intn()`
   4. `Int63()`
4. `strings` 包实现了用于操作字符的简单函数
   1. `strings.Builder`
   2. `WriteByte()`
   3. `String()`

## 结论

1. 如果字符串是英文字母大小写，可以用 `byte` 代替 `rune`，速度提升 22% ，内存分配减少了 67%
2. 用 `rand.Int63()` 代替 `rand.Intn()` 速度提升 21%
3. 使用位运算性能下降了 22% ，但是充分利用 `rand.Int63()` 速度可提升 3 倍
4. 使用 `rand.Source()` 代替 `rand.Rand()` 速度提升 17%
5. 使用 `strings.Builder` 速度提升不大，但是内存分配减少 50%
6. 使用 `unsafe` 代替 `strings.Builder` 速度提升 14%
7. 优化到最后，与第一个生成随机字符串的方法对比可发现速度提升了 6 倍，内存分配减少了 83%

```bash
goos: darwin
goarch: amd64
pkg: study/cmd
BenchmarkRunes-8                         2000000               568 ns/op              96 B/op          2 allocs/op
BenchmarkBytes-8                         3000000               442 ns/op              32 B/op          2 allocs/op
BenchmarkBytesRmndr-8                    5000000               347 ns/op              32 B/op          2 allocs/op
BenchmarkBytesMask-8                     3000000               422 ns/op              32 B/op          2 allocs/op
BenchmarkBytesMaskImpr-8                10000000               138 ns/op              32 B/op          2 allocs/op
BenchmarkBytesMaskImprSrc-8             20000000               115 ns/op              32 B/op          2 allocs/op
BenchmarkBytesMaskImprSrcSB-8           20000000               109 ns/op              16 B/op          1 allocs/op
BenchmarkBytesMaskImprSrcUnsafe-8       20000000                94.3 ns/op            16 B/op          1 allocs/op
PASS
ok      study/cmd       15.894s
```

## 随机生成固定长度字符串方法

> random.go 文件
> 以下代码来自于参考链接

```go
package main

import (
	"math/rand"
	"strings"
	"time"
	"unsafe"
)

// Implementations

func init() {
	rand.Seed(time.Now().UnixNano())
}

var letterRunes = []rune("abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ")

func RandStringRunes(n int) string {
	b := make([]rune, n)
	for i := range b {
		b[i] = letterRunes[rand.Intn(len(letterRunes))]
	}
	return string(b)
}

const letterBytes = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ"
const (
	letterIdxBits = 6                    // 6 bits to represent a letter index
	letterIdxMask = 1<<letterIdxBits - 1 // All 1-bits, as many as letterIdxBits
	letterIdxMax  = 63 / letterIdxBits   // # of letter indices fitting in 63 bits
)

func RandStringBytes(n int) string {
	b := make([]byte, n)
	for i := range b {
		b[i] = letterBytes[rand.Intn(len(letterBytes))]
	}
	return string(b)
}

func RandStringBytesRmndr(n int) string {
	b := make([]byte, n)
	for i := range b {
		b[i] = letterBytes[rand.Int63()%int64(len(letterBytes))]
	}
	return string(b)
}

func RandStringBytesMask(n int) string {
	b := make([]byte, n)
	for i := 0; i < n; {
		if idx := int(rand.Int63() & letterIdxMask); idx < len(letterBytes) {
			b[i] = letterBytes[idx]
			i++
		}
	}
	return string(b)
}

func RandStringBytesMaskImpr(n int) string {
	b := make([]byte, n)
	// A rand.Int63() generates 63 random bits, enough for letterIdxMax letters!
	for i, cache, remain := n-1, rand.Int63(), letterIdxMax; i >= 0; {
		if remain == 0 {
			cache, remain = rand.Int63(), letterIdxMax
		}
		if idx := int(cache & letterIdxMask); idx < len(letterBytes) {
			b[i] = letterBytes[idx]
			i--
		}
		cache >>= letterIdxBits
		remain--
	}

	return string(b)
}

var src = rand.NewSource(time.Now().UnixNano())

func RandStringBytesMaskImprSrc(n int) string {
	b := make([]byte, n)
	// A src.Int63() generates 63 random bits, enough for letterIdxMax characters!
	for i, cache, remain := n-1, src.Int63(), letterIdxMax; i >= 0; {
		if remain == 0 {
			cache, remain = src.Int63(), letterIdxMax
		}
		if idx := int(cache & letterIdxMask); idx < len(letterBytes) {
			b[i] = letterBytes[idx]
			i--
		}
		cache >>= letterIdxBits
		remain--
	}

	return string(b)
}

func RandStringBytesMaskImprSrcSB(n int) string {
	sb := strings.Builder{}
	sb.Grow(n)
	// A src.Int63() generates 63 random bits, enough for letterIdxMax characters!
	for i, cache, remain := n-1, src.Int63(), letterIdxMax; i >= 0; {
		if remain == 0 {
			cache, remain = src.Int63(), letterIdxMax
		}
		if idx := int(cache & letterIdxMask); idx < len(letterBytes) {
			sb.WriteByte(letterBytes[idx])
			i--
		}
		cache >>= letterIdxBits
		remain--
	}

	return sb.String()
}

func RandStringBytesMaskImprSrcUnsafe(n int) string {
	b := make([]byte, n)
	// A src.Int63() generates 63 random bits, enough for letterIdxMax characters!
	for i, cache, remain := n-1, src.Int63(), letterIdxMax; i >= 0; {
		if remain == 0 {
			cache, remain = src.Int63(), letterIdxMax
		}
		if idx := int(cache & letterIdxMask); idx < len(letterBytes) {
			b[i] = letterBytes[idx]
			i--
		}
		cache >>= letterIdxBits
		remain--
	}

	return *(*string)(unsafe.Pointer(&b))
}

```

## 基准测试代码

> 新建 `random_test.go` 文件，执行测试命令 `go test  -benchmem study/cmd -bench .`
> 以下代码来自于参考链接

```go
package main

import "testing"

// Benchmark functions
const n = 16

func BenchmarkRunes(b *testing.B) {
	for i := 0; i < b.N; i++ {
		RandStringRunes(n)
	}
}

func BenchmarkBytes(b *testing.B) {
	for i := 0; i < b.N; i++ {
		RandStringBytes(n)
	}
}

func BenchmarkBytesRmndr(b *testing.B) {
	for i := 0; i < b.N; i++ {
		RandStringBytesRmndr(n)
	}
}

func BenchmarkBytesMask(b *testing.B) {
	for i := 0; i < b.N; i++ {
		RandStringBytesMask(n)
	}
}

func BenchmarkBytesMaskImpr(b *testing.B) {
	for i := 0; i < b.N; i++ {
		RandStringBytesMaskImpr(n)
	}
}

func BenchmarkBytesMaskImprSrc(b *testing.B) {
	for i := 0; i < b.N; i++ {
		RandStringBytesMaskImprSrc(n)
	}
}
func BenchmarkBytesMaskImprSrcSB(b *testing.B) {
	for i := 0; i < b.N; i++ {
		RandStringBytesMaskImprSrcSB(n)
	}
}

func BenchmarkBytesMaskImprSrcUnsafe(b *testing.B) {
	for i := 0; i < b.N; i++ {
		RandStringBytesMaskImprSrcUnsafe(n)
	}
}

```

## 参考

- [how to generate a random string of a fixed length in go](https://stackoverflow.com/questions/22892120/how-to-generate-a-random-string-of-a-fixed-length-in-go)

---
