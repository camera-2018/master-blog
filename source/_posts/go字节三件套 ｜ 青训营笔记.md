---
title: go字节三件套 ｜ 青训营笔记
date: 2023-8-28 22:26:29
tags: 青训营笔记
---

## Gorm、Kitex、Hertz：三件套介绍与基本用法
### Gorm

Gorm是Golang中广受欢迎的ORM（对象关系映射）框架，已经发展数十年，具有强大的功能和出色的性能。

ORM框架用于将面向对象的概念与数据库中的表相对应，简化数据操作过程。在Golang中，自定义的结构体与数据库表一一对应，结构体的实例对应表中的一条记录。

基本用法

定义结构体：在Gorm中，定义结构体来映射数据库表。
```go
type User struct {
  ID    uint   `gorm:"primary_key"`
  Name  string `gorm:"type:varchar(100)"`
  Email string `gorm:"type:varchar(100);uniqueIndex"`
}
```
增加数据：使用Create方法来新增数据条目。
```go
db.Create(&User{Name: "John", Email: "john@example.com"})
```
查询数据：使用Find进行数据查找，可以通过Where和Or条件构建查询条件。
```go
var user User
db.Where("name = ?", "John").First(&user)
```
更新数据：使用Update进行数据更新，可以使用Model结合Update或Updates方法来更新列。
```go
db.Model(&user).Update("Name", "John Updated")
```
删除数据：使用Delete进行数据删除操作，根据是否包含gorm.deletedat字段执行物理或逻辑删除。

```go
db.Delete(&user)
```
### Kitex

https://www.cloudwego.io/zh/docs/kitex/overview/

Kitex是字节开发的高性能Golang微服务RPC框架，具备出色的可扩展性和高性能。
服务端
```go
func main() {
  srv := kitex.NewServer(new(YourServiceImpl), 
                          kitex.WithServiceAddr(":8888"))
  srv.Run()
}

```
客户端
```go
func main() {
  client := NewYourServiceClient("127.0.0.1:8888", kitex.WithTransportProtocol(protocol.TRPC))
  // 调用RPC方法
}

```
高性能：Kitex具有优异的性能表现，适用于高负载的微服务场景。
可扩展：支持多协议，并且有丰富的开源扩展库，可以满足各种需求。

### Hertz

https://www.cloudwego.io/zh/docs/hertz/overview/



Hertz是字节开发的HTTP框架，结合了其他开源框架的优点，同时满足字节跳动内部的需求，具有高可用性、高性能和高扩展性。
```go
package main

import (
    "context"

    "github.com/cloudwego/hertz/pkg/app"
    "github.com/cloudwego/hertz/pkg/app/server"
    "github.com/cloudwego/hertz/pkg/common/utils"
    "github.com/cloudwego/hertz/pkg/protocol/consts"
)

func main() {
    h := server.Default()

    h.GET("/ping", func(c context.Context, ctx *app.RequestContext) {
            ctx.JSON(consts.StatusOK, utils.H{"message": "pong"})
    })

    h.Spin()
}

```

高可用性：Hertz框架被设计为具有高度的稳定性和可用性，适用于大规模的应用场景。
高性能：框架在性能方面表现出色，适合处理高并发请求。
高扩展性：Hertz框架允许根据业务需求进行定制和扩展，以满足复杂应用的要求。

