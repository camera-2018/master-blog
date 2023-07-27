---
title: go语言三个小项目 ｜ 青训营笔记
date: 2023-7-25 19:59:09
tags: 青训营笔记
---

## 猜数字

1. 猜数字
这个项目非常简单，它涉及到随机数的生成和用户输入操作。
我们使用了bufio库来处理输入数据。

```go
reader := bufio.NewReader(os.Stdin)
input, _ := reader.ReadString('\n')
```

## 简单字典
2. 标准库strconv
它主要用于字符和其他类型之间的转换。
```go
strconv.Atoi(s string) int 
```

3. 标准库strings
```go
strings.TrimSuffix(s string ,  suffix string) string
```
删除末尾字符，如果没有就正常返回

4. 网络库
```go
client := &http.Client{}
```
初始化请求客户端
```go
req,  err := http.NewRequest("POST", "https://api.interpreter.caiyunai.com/v1/dict",  data)
```
构造头
```go
req.Header.Set("authority",  "api.interpreter.caiyunai.com")
```
请求
```go
bodyText,  err := io.ReadAll(resp.Body)
err = json.Unmarshal(bodyText,  &dictResponse)
```

5. os库

```go
word := os.Args[1]
```
获取环境参数

## socks5代理

```go
package main

import (
	"context"
	"encoding/binary"
	"fmt"
	"io"
	"net"
)

const (
	socks5Ver  = 0x05
	atypeIPV4  = 0x01
	atypeHOST  = 0x03
	cmdConnect = 0x01
)

func main() {
	// 假设在这里建立一个 net.Listener 监听 Socks5 代理请求

	for {
		client, err := acceptConnection()
		if err != nil {
			continue
		}

		// 开启一个 goroutine 处理客户端请求
		go handleSocks5Client(client)
	}
}

func acceptConnection() (net.Conn, error) {
	// 实现接收连接请求的逻辑
}

func handleSocks5Client(conn net.Conn) {
	defer conn.Close()

	// 鉴权阶段
	if err := auth(conn); err != nil {
		fmt.Println("Authentication error:", err)
		return
	}

	// 通讯阶段
	addr, err := connect(conn)
	if err != nil {
		fmt.Println("Connection error:", err)
		return
	}

	// 给客户端回包，表示连接成功
	_, _ = conn.Write([]byte{socks5Ver, 0x00, 0x00, atypeIPV4, 0, 0, 0, 0, 0, 0})

	// 开始进行数据转发
	dest, err := net.Dial("tcp", addr)
	if err != nil {
		fmt.Println("Failed to connect to destination:", err)
		return
	}
	defer dest.Close()

	ctx, cancel := context.WithCancel(context.Background())
	defer cancel()

	// 从客户端到目标地址的数据拷贝
	go func() {
		_, _ = io.Copy(dest, conn)
		cancel()
	}()

	// 从目标地址到客户端的数据拷贝
	go func() {
		_, _ = io.Copy(conn, dest)
		cancel()
	}()

	<-ctx.Done()
}

func auth(conn net.Conn) error {
	buf := make([]byte, 2)
	_, err := io.ReadFull(conn, buf)
	if err != nil {
		return err
	}

	ver, nmethods := buf[0], buf[1]
	methods := make([]byte, nmethods)
	_, err = io.ReadFull(conn, methods)
	if err != nil {
		return err
	}

	// 在这里根据收到的 methods 进行认证处理

	// 假设这里选择不需要认证，回包告知客户端不需要认证
	_, err = conn.Write([]byte{socks5Ver, 0x00})
	if err != nil {
		return err
	}

	return nil
}

func connect(conn net.Conn) (string, error) {
	buf := make([]byte, 4)
	_, err := io.ReadFull(conn, buf)
	if err != nil {
		return "", err
	}

	ver, cmd, _, atyp := buf[0], buf[1], buf[2], buf[3]
	if ver != socks5Ver || cmd != cmdConnect {
		return "", fmt.Errorf("Unsupported SOCKS5 command")
	}

	var addr string
	switch atyp {
	case atypeIPV4:
		buf := make([]byte, 4)
		_, err := io.ReadFull(conn, buf)
		if err != nil {
			return "", err
		}
		addr = fmt.Sprintf("%d.%d.%d.%d", buf[0], buf[1], buf[2], buf[3])

	case atypeHOST:
		hostSizeBuf := make([]byte, 1)
		_, err := io.ReadFull(conn, hostSizeBuf)
		if err != nil {
			return "", err
		}
		hostSize := int(hostSizeBuf[0])
		hostBuf := make([]byte, hostSize)
		_, err = io.ReadFull(conn, hostBuf)
		if err != nil {
			return "", err
		}
		addr = string(hostBuf)
	}

	portBuf := make([]byte, 2)
	_, err = io.ReadFull(conn, portBuf)
	if err != nil {
		return "", err
	}
	port := binary.BigEndian.Uint16(portBuf)

	return fmt.Sprintf("%v:%v", addr, port), nil
}

```

使用 SOCKS5 代理的实现，涵盖了以下主要步骤：

鉴权阶段：

解析客户端发送的版本号、支持的认证方法数量和认证方法列表。
针对支持的认证方法进行验证，返回认证成功的回包。
通讯阶段：

解析客户端发送的连接请求，包括版本号、命令类型、目标地址类型、目标地址、目标端口等信息。
根据目标地址类型和地址解析方式，获取目标地址和端口号。
向目标地址发起连接请求，并返回连接成功的回包。
进程管理：

使用 goroutine 后台开启两个服务器交互进程。
通过 context 库进行进程管理，确保进程在必要时可以被关闭。
将客户端和目标之间的数据互相拷贝，使得数据能够在两者之间传递。


## 四、课后个人总结：

在这节课学习的内容中，我收获了很多关于Go语言编程的知识和技能。以下是我的感想：

Go语言的简洁和高效：通过学习这些基础标准库和相关功能，我深刻感受到Go语言的简洁和高效。标准库提供了丰富的功能，让编程变得更加简单和高效。

数据类型转换和字符串处理：学习了strconv和strings包，对于数据类型转换和字符串处理有了更深入的了解。这些工具使得在处理用户输入和数据转换时更加方便。

网络编程和HTTP库：了解了Go语言中的网络编程和net/http包，我现在能够编写简单的HTTP服务器和客户端，这对于开发网络应用和服务端程序非常有用。

文件操作和系统交互：通过os包的学习，我学会了如何在Go语言中进行文件操作和与操作系统进行交互，这对于处理文件和系统配置十分重要。

代理和鉴权：学习了socks5代理和鉴权机制，我对网络代理和安全认证有了更深刻的理解，这对于开发安全性较高的应用非常重要。

上下文处理：context包的学习使我了解了在Go语言中如何优雅地处理请求上下文，更好地控制请求的流程和生命周期。