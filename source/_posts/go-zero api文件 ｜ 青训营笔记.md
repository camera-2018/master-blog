---
title: go-zero api文件生成项目框架 ｜ 青训营笔记
date: 2023-8-30 22:14:03
tags: 青训营笔记
---

## go-zero api文件生成项目框架

## 数据结构

.api文件是go-zero自创的文件格式，和protobuf的语法有很大不同，但好在不难理解。具体语法介绍可看官网文档：[go-zero.dev/docs/tasks/…](https://link.juejin.cn?target=https%3A%2F%2Fgo-zero.dev%2Fdocs%2Ftasks%2Fdsl%2Fapi)

先编写接口定义，然后直接一次性使用 goctl 生成代码。

api文件支持将一个数据结构嵌入另一个结构中，便于编写统一的响应格式。

首先定义空请求结构和基础的响应结构：

```api
syntax = "v1"  
  
type Empty {  
}  
  
type BasicResponse {  
    StatusCode int32 `json:"status_code"`  
    StatusMsg string `json:"status_msg"`  
}
```

type关键字也支持代码块，只需要用`type()`包裹，就可以省去关键词。下面定义了注册和登录的请求和响应结构。

```go
type (  
    RegisterRequest struct {  
        Username string `form:"username"`  
        Password string `form:"password"`  
    }  

    RegisterResponse struct {  
        BasicResponse  
        UserId int64 `json:"user_id"`   
    }  
)  
  
type (  
    LoginRequest struct {  
        Username string `form:"username"`  
        Password string `form:"password"`  
    }  

    LoginResponse struct {  
        BasicResponse  
        UserId int64 `json:"user_id"`  
    }  
)
```

最后还要定义一个获取用户信息的接口。因为本文不涉及创作、社交方面的业务逻辑实现，所以`User`结构其实没什么可以返回的信息。之后将其嵌入响应类型的`user`字段中：

```go
type (  
    User {  
        Id int64 `json:"id"`  
        Name string `json:"name"`  
    }  
    
    GetUserInfoRequest struct {  
        UserId int64 `form:"user_id"`   
    }  

    GetUserInfoResponse struct {  
        BasicResponse  
        User User `json:"user"`  
    }  
)
```

## 定义接口路由

把所有api路由配置好

```api
@server(  
    group: app  
)  
service app {  
    @handler Ping  
    get /ping (Empty) returns (BasicResponse)  
}  
  
@server(  
    group: user  
    prefix: /douyin/user  
)  
service app {  
    @handler Register  
    post /register (RegisterRequest) returns (RegisterResponse)  
  
    @handler Login  
    post /login (LoginRequest) returns (LoginResponse)  
  
    @handler GetUserInfo  
    get / (GetUserInfoRequest) returns (GetUserInfoResponse)  
}
```

以下几点需要注意：

1. 如果service代码块有多个，要求后面的名称（即“app”）相同
2. 形如装饰器的@server代码块可以做代码分组、路由前缀、鉴权、中间件等多种配置。
   - 这里把路由分成app和user两组，之后生成时会分组放在文件夹里
   - 给user组设置了`/douyin/user`的路由器前缀，就不用重复写了
   - 鉴权和中间件配置下文会提到
3. 每个接口需要一个`handler`，其实就是给函数起名
4. 接口定义的格式是 `<HTTP方法> <子路径> (<请求数据结构>) returns (<响应数据结构>) `

## 创建项目模板

这次不是新建示例项目，而是根据现有的api声明创建

安装 goctl

https://go-zero.dev/docs/tasks/installation/goctl

```bash
$ GO111MODULE=on go install github.com/zeromicro/go-zero/tools/goctl@latest
```

安装 protoc

https://go-zero.dev/docs/tasks/installation/protoc

```bash
$ goctl env check --install --verbose --force
```



```shell
goctl api go --api app.api --dir=. --style=goZero 
```

这会在当前目录生成项目文件。之后进入目录安装依赖：

```shell
go mod tidy
```

## 测试Ping接口

Ping接口的逻辑在`internal/logic/app/pingLogic.go`，打开文件并编辑handler函数：

```go
func (l *PingLogic) Ping(req *types.Empty) (resp *types.BasicResponse, err error) {  
    return &types.BasicResponse{
        StatusCode: 0,
        StatusMsg:  "pong",
    }, nil
}
```

这里的写法和rpc微服务有点区别。注意到传入的`resp`是一个指针，所以需要**手动创建**一个响应结构体并设置键值。

这样就算完成Ping接口了，可以运行服务。

