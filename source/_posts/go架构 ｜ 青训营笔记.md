---
title: go架构 ｜ 青训营笔记
date: 2023-8-24 18:35:56
tags: 青训营笔记
---

## 什么是架构

主要针对互联网服server系统（类似网站）来定义架构：架构是系统的骨架，支撑和链接各个部分，包括组件、连接件、约束规范，以及指导这些内容设计与演化的原理。

- 组件：类似应用服务，独立模块、数据库、nginx等等、

- 连接件：分布式调用、进程间调用、调用使用http协议还是tcp协议、组件之间的交互关系、

- 约束规范： 定规则做限制：例如设计原则、编码规范等等。

### 组件

组件是架构的构建块，包括独立模块、数据库、Nginx等。

例如，Go语言中可以定义一个组件，如一个HTTP服务器：

```go
package main

import (
	"net/http"
)

func main() {
	http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
		w.Write([]byte("Hello, World!"))
	})
	http.ListenAndServe(":8080", nil)
}
```

### 连接件

连接件处理组件之间的交互关系，例如分布式调用、进程间调用、使用的协议等。

### 约束规范

约束规范定义了架构中的规则和限制，例如设计原则和编码规范。

## 几种典型架构

### 单机架构

单机架构就是把所有功能都实现在一个进程中，并部署在一台机器上。

单机架构的**优点**是非常简单，**缺点**是会遇到并发处理的难题，而且运维需要停服。

当时计算机软硬件性能比较基础，这个问题引起人们对网络套接字优化和服务端调度的关注，这个词语也随着时代的发展逐渐变化，如`C10M`代表的是处理1000万个并发连接。现在的计算机已经有足够的性能和优秀的软件来支撑单机数百万连接了，然而并发问题仍然是后端架构设计中不能略过的关键问题。

### 单体架构

单体架构是对单机架构的改进。既然单机处理能力有限，就可以采用分布式部署的模式设置多台单机，再设置一个分流环节，将请求转发给各个单体服务器来处理。

单体架构的优点是可以实现水平扩容，处理能力不够时只需要简单添加新的单机即可；运维不需要停服，可以逐个对单机维护不影响其他单机的运行。单体架构下，每台机器的功能是一致的，因此它没有解决这两个缺点：

1. 职责太多，开发效率低：每台机器都要实现系统的完整功能，不能专注于某个功能的开发。
2. **爆炸半径大**： 爆炸半径是指爆炸可能造成损害的区域范围。在生产环境中指的就是部署的服务故障时可能影响的软件功能范围。单体架构下每一个单机下线，都会造成所有功能的处理承载量下降，因此爆炸半径覆盖了软件多数功能。

### 垂直应用架构

为了解决单体架构的问题，垂直应用架构提供了一种思路，将系统按照服务拆分任务，分配给不同的服务器，从而实现职责的简单划分，一定程度上提高开发和运维的效率。不过在实践中一个服务往往是大量功能点的集合，仍然存在功能琐碎的问题

## 更加高级的架构

### SOA、微服务架构

SOA（Service-Oriented Architecture）架构具有两个特性：

1. 将应用的不同功能单元抽象为服务，从而细分职责
2. 定义服务之间的通信标准，确保服务之间的数据流通

微服务架构则是SOA架构去中心化的演进方向，旨在减少服务之间的沟通消耗，避免SOA服务内部集中沟通导致的过度中心化问题，他实现了水平切分，减少了跨级别调用链，让每个服务直接负责的上下级减少。

这类架构在解决之前的弊端时，也会引入新的问题：

- 数据一致性问题：不同服务如何确保数据同步
- 高可用问题：服务之间如何进行可靠合作
- 治理问题：一个服务出现问题时，要如何容灾
- 解耦和过微问题：过度细分会导致运维成本提高，要如何权衡？

### 事件驱动架构

事件驱动架构（EDA）是一种异步架构，其中生产者生成事件，并由一个或多个消费者消费。这允许解耦生产者和消费者，并实现高度可扩展和灵活的系统。

Apache Kafka
Apache Kafka是一种流行的事件流平台。您可以使用Go编写Kafka的生产者和消费者。

如下是示例代码 

```go
package main

import (
	"github.com/confluentinc/confluent-kafka-go/kafka"
)

func main() {
	producer, err := kafka.NewProducer(&kafka.ConfigMap{"bootstrap.servers": "localhost"})
	if err != nil {
		panic(err)
	}

	// 生产消息
	producer.Produce(&kafka.Message{
		TopicPartition: kafka.TopicPartition{Topic: &topic, Partition: kafka.PartitionAny},
		Value:          []byte("Message Value"),
	}, nil)

	// 消费消息
	consumer, err := kafka.NewConsumer(&kafka.ConfigMap{
		"bootstrap.servers": "localhost",
		"group.id":          "myGroup",
		"auto.offset.reset": "earliest",
	})
	consumer.SubscribeTopics([]string{"myTopic"}, nil)
	msg, err := consumer.ReadMessage(-1)
	if err == nil {
		fmt.Printf("Message on %s: %s\n", msg.TopicPartition, string(msg.Value))
	}
}

```

### 云计算

云计算是指通过软件自动化管理，提供计算资源的服务网络，是现在互联网大规模数据分析和存储的基石。

云计算的基础是虚拟化技术和编排方式，关系到资源的分配和调度。

云计算提供一系列特有的架构：

- 基础设施即服务IaaS（Infrastructure as a Service）：服务商提供互联网基础设施，比如用户只需要获得服务器、云硬盘、宽带网络，而不用关心物理的机房、数据中心、网络怎么建设。但是，用户需要自己进行服务器系统安装、环境部署和软件配置。
- 平台即服务PaaS（Platform as a Service）：服务商提供底层软件设施，比如为用户准备安装了指定操作系统、数据库软件、环境套件的主机。用户需要自己控制上层应用的部署和托管。
- 软件即服务Saas（Software as a Service）：服务商及提供基于软件的解决方案来满足客户的需求，比如用户想要一套OA办公系统，CMS内容管理系统，HRM人事管理系统等，而不用自行搭建和维护这样的服务，能够简化用户的工作。
- 函数即服务FaaS（Function as a service）：服务商提供一个平台，允许客户开发、运行和管理应用程序功能，而不用关心如何启动和部署这样的程序。这个模式是“无服务器”体系架构的方式，具有灵活性，适合构建微服务应用程序。

### 云原生架构

云原生技术为公司在公有云、私有云、混合云等新型动态环境中，构建和运行可弹性拓展的应用提供了可能。

云原生为公司提供了以下关键能力：

### 弹性资源调度

- 服务资源调度：分配微服务、大服务使用的资源。
- 计算资源调度：分配在线计算、离线计算使用的资源
- 消息队列：和计算资源调度类似，提供海量的数据吞吐和大数据分析能力。

### 弹性存储资源

云原生让用户可以将存储资源看作服务，寻找对应的服务来满足自己的存储需求。

- 经典存储：对象存储、大数据记录存储。
- 关系型数据库：业务记录存储。
- 元数据：提供服务发现能力
- NoSQL：key-value存储，实现缓存和分布式事务。

### DevOps

DevOps贯穿整个软件开发周期，使用一系列自动化流程，提高软件开发、交付的效率。DevOps提供了敏捷开发、持续集成/交付（CI/CD）的能力。

### 微服务架构

微服务架构实现业务功能单元的解耦，同时提供一套统一的通信标准确保服务之间的数据流通。

微服务常见的通信标准是HTTP（RESTful API）和RPC（Thrift, gRPC），需要结合性能、服务治理、协议可解释性来选择。实际场景下，常常使用微服务框架提供的通信能力，而不用自己从头实现一套。

### 服务网格

服务网格实现业务与治理的结构以及异构系统治理的统一化，从而提供复杂的治理能力。

### 服务发现

服务注册与发现 Eureka或Consul等服务可以用于服务发现。

服务注册与发现使服务可以动态地找到其他服务的位置。
```go
// 使用Consul进行服务注册
consulConfig := consulapi.DefaultConfig()
consulConfig.Address = "localhost:8500"
client, err := consulapi.NewClient(consulConfig)
if err != nil {
	log.Fatal(err)
}

// 创建新的服务
registration := new(consulapi.AgentServiceRegistration)
registration.ID = "my-service-id"
registration.Name = "my-service"
registration.Port = 8080
registration.Tags = []string{"my-service"}

// 注册服务
agent := client.Agent()
if err := agent.ServiceRegister(registration); err != nil {
	log.Fatal(err)
}

// 服务发现逻辑...

```

### 分布式架构

分布式锁

在分布式环境中，协调多个节点的访问资源可能会变得复杂。分布式锁是一种解决方案，可以在多个节点之间同步访问。

可以使用Go语言和Redis创建分布式锁：

```go

package main

import (
	"fmt"
	"github.com/go-redis/redis/v8"
	"golang.org/x/net/context"
)

func main() {
	rdb := redis.NewClient(&redis.Options{
		Addr: "localhost:6379",
	})

	ctx := context.Background()
	lockKey := "lock_key"

	// 尝试获取锁
	locked, err := rdb.SetNX(ctx, lockKey, "lock_value", 0).Result()
	if err != nil || !locked {
		fmt.Println("获取锁失败")
		return
	}

	// 执行业务逻辑...

	// 释放锁
	rdb.Del(ctx, lockKey)
	fmt.Println("业务逻辑执行完毕，锁已释放")
}

```

## 优化方式

### 离在线资源并池

在线业务以**IO密集型**为主，要求计算的**实时性**，但是具有**潮汐性**，不同时间段的压力不同。离线业务以**计算密集型**为主，对计算实时性不高。

把在线和离线资源放入同一个资源池进行调度，利用在线业务的潮汐性自动扩缩容，能够有效降低物理资源成本，提供更多的弹性资源，增加收益。

### 负载均衡

简单的http负载均衡 轮询算法

```go
package main

import (
	"net/http"
	"net/http/httputil"
	"net/url"
)

var servers = []string{
	"http://localhost:8081",
	"http://localhost:8082",
}

var currentServer = 0

func nextServer() string {
	server := servers[currentServer]
	currentServer = (currentServer + 1) % len(servers)
	return server
}

func main() {
	http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
		server := nextServer()
		target, _ := url.Parse(server)
		proxy := httputil.NewSingleHostReverseProxy(target)
		proxy.ServeHTTP(w, r)
	})
	http.ListenAndServe(":8080", nil)
}

```

### 亲和性部署

如果两个微服务之间通信密切，则这两个服务的亲和性较高。将满足亲和性条件的容器调度到一台宿主机，让微服务中间件与服务网格通过共享内存通信，能够有效降低通信成本，提高服务可用性。

### 流量治理

基于微服务中间件和服务网格的流量治理，赋予微服务熔断、重试的能力，提供复杂环境下的流量调度，可以提高微服务调用容错性，增强容灾能力，还能进一步提高开发效率。

### CPU水位负载均衡

结合IaaS提供的资源探针和服务网格的负载均衡能力，能够为自动扩缩容提供反馈，打平异构环境下算力的差异。

### 容器化与服务编排

Docker

可以使用Docker将Go应用程序打包到容器中，并确保它在任何支持Docker的平台上以相同的方式运行。

Kubernetes

Kubernetes是一种强大的容器编排工具，用于自动部署、扩展和管理容器化应用程序。Go语言是Kubernetes的主要实现语言。


## 总结

现代软件架构的复杂性和灵活性要求了解许多概念和技术。从单机到分布式，从单体到微服务，架构演化为满足不断增长的需求。

通过使用强大和灵活的编程语言，如Go，您可以构建符合这些需求的解决方案。从分布式锁到服务发现，从负载均衡到微服务通信，Go为构建现代、可扩展和高性能的系统提供了强大的工具和库。

不断学习和实践这些概念和技术将使您更好地设计和构建复杂的系统，满足业务目标，并应对未来的挑战。