---
title: gorm 初体验 ｜ 青训营笔记
date: 2023-8-23 21:50:26
tags: 青训营笔记
---

## gorm
![](https://gorm.io/gorm.svg)

gorm是Golang语言中一款性能极好的ORM库，对开发人员相对是比较友好的。接下来主要学习下gorm库的一些基本使用。

功能概览

- 全功能 ORM
- 关联 (Has One，Has Many，Belongs To，Many To Many，多态，单表继承)
- Create，Save，Update，Delete，Find 中钩子方法
- 支持 Preload、Joins 的预加载
- 事务，嵌套事务，Save Point，Rollback To Saved Point
- Context、预编译模式、DryRun 模式
- 批量插入，FindInBatches，Find/Create with Map，使用 SQL 表达式、Context Valuer 进行 CRUD
- SQL 构建器，Upsert，数据库锁，Optimizer/Index/Comment Hint，命名参数，子查询
复合主键，索引，约束
- Auto Migration
- 自定义 Logger
- 灵活的可扩展插件 API：Database Resolver（多数据库，读写分离）、Prometheus…
- 每个特性都经过了测试的重重考验
- 开发者友好

### 安装

安装
``` bash
go get -u gorm.io/gorm
go get -u gorm.io/driver/sqlite
```

### 模型定义
#### 基本定义
因为orm可以自动创建数据库，创建表，所以我们要用一个约定的形式来描述一个schema，便于他创建表，也便于代码中调用

例如
``` go
type User struct {
  Name         string
  Email        string
  Age          uint8
}
```

这样的一个结构体就可以描述user这个表

#### gorm.Model
``` go
// gorm.Model 的定义
type Model struct {
  ID        uint           `gorm:"primaryKey"`
  CreatedAt time.Time
  UpdatedAt time.Time
  DeletedAt gorm.DeletedAt `gorm:"index"`
}
```
这个约定了一些gorm的标准配置，相当于implement了这个结构体

例如
``` go
type User struct {
  gorm.Model
  Name         string
  Email        string
  Age          uint8
}
```


这样这个gorm.Model相当于在user表里添加了一些基础的字段

这个user最终会变成 相当于

``` go
type User struct {
  ID        uint           `gorm:"primaryKey"`
  CreatedAt time.Time
  UpdatedAt time.Time
  DeletedAt gorm.DeletedAt `gorm:"index"`
  Name         string
  Email        string
  Age          uint8
}
```

#### 嵌入结构体
如上的gorm.Model就是一种嵌入结构体

官网的解释是：对于匿名字段，GORM 会将其字段包含在父结构体中

其他嵌入方法为：

对于正常的结构体字段，你也可以通过标签 embedded 将其嵌入，例如：
```go
type Author struct {
    Name  string
    Email string
}

type Blog struct {
  ID      int
  Author  Author `gorm:"embedded"`
  Upvotes int32
}
```

```go
// 它等效于
type Blog struct {
  ID    int64
  Name  string
  Email string
  Upvotes  int32
}
```

> 可以使用标签 embeddedPrefix 来为 db 中的字段名添加前缀
> Author  Author `gorm:"embedded;embeddedPrefix:author_"



#### 字段标签

声明 model 时，tag 是可选的，gorm 支持以下这些 tag： tag 名大小写不敏感，但我们写的时候最好用驼峰命名法。

写法为 在字段后面添加飘号和gorm:

例如
<pre>
ID        uint           `gorm:"primaryKey"`
</pre>

| 标签名                 | 说明                                                         |
| :--------------------- | :----------------------------------------------------------- |
| column                 | 指定 database 的列名                                         |
| type                   | 列数据类型，推荐使用兼容性好的通用类型，例如：所有数据库都支持 bool、int、uint、float、string、time、bytes 并且可以和其他标签一起使用，例如：`not null`、`size`, `autoIncrement`… 像 `varbinary(8)` 这样指定数据库数据类型也是支持的。在使用指定数据库数据类型时，它需要是完整的数据库数据类型，如：`MEDIUMINT UNSIGNED not NULL AUTO_INCREMENT` |
| serializer             | 指定将数据序列化或反序列化到数据库中的序列化器, 例如: `serializer:json/gob/unixtime` |
| size                   | 定义列数据类型的大小或长度，例如 `size: 256`                 |
| primaryKey             | 将列定义为主键                                               |
| unique                 | 将列定义为唯一键                                             |
| default                | 定义列的默认值                                               |
| precision              | 指定列的精度                                                 |
| scale                  | 指定列大小                                                   |
| not null               | 指定列为 NOT NULL                                            |
| autoIncrement          | 指定列为自动增长                                             |
| autoIncrementIncrement | 自动步长，控制连续记录之间的间隔                             |
| embedded               | 嵌套字段                                                     |
| embeddedPrefix         | 嵌入字段的列名前缀                                           |
| autoCreateTime         | 创建时追踪当前时间，对于 `int` 字段，它会追踪时间戳秒数，您可以使用 `nano`/`milli` 来追踪纳秒、毫秒时间戳，例如：`autoCreateTime:nano` |
| autoUpdateTime         | 创建/更新时追踪当前时间，对于 `int` 字段，它会追踪时间戳秒数，您可以使用 `nano`/`milli` 来追踪纳秒、毫秒时间戳，例如：`autoUpdateTime:milli` |
| index                  | 根据参数创建索引，多个字段使用相同的名称则创建复合索引，查看 [索引](https://gorm.io/zh_CN/docs/indexes.html) 获取详情 |
| uniqueIndex            | 与 `index` 相同，但创建的是唯一索引                          |
| check                  | 创建检查约束，例如 `check:age > 13`，查看 [约束](https://gorm.io/zh_CN/docs/constraints.html) 获取详情 |
| <-                     | 设置字段写入的权限， `<-:create` 只创建、`<-:update` 只更新、`<-:false` 无写入权限、`<-` 创建和更新权限 |
| ->                     | 设置字段读的权限，`->:false` 无读权限                        |
| -                      | 忽略该字段，`-` 表示无读写，`-:migration` 表示无迁移权限，`-:all` 表示无读写迁移权限 |
| comment                | 迁移时为字段添加注释                                         |

### 连接数据库

官方提供的代码
``` go
import (
  "gorm.io/driver/mysql"
  "gorm.io/gorm"
)

func main() {
  // 参考 https://github.com/go-sql-driver/mysql#dsn-data-source-name 获取详情
  dsn := "user:pass@tcp(127.0.0.1:3306)/dbname?charset=utf8mb4&parseTime=True&loc=Local"
  db, err := gorm.Open(mysql.Open(dsn), &gorm.Config{})
}

```

这里我提供一种封装方案，因为一般数据库配置写在配置文件里，防止泄露与主代码分离‘


```go

import (
	"fmt"
	"gorm.io/driver/mysql"
	"gorm.io/gorm"
	"gorm.io/gorm/schema"
)

type Config struct {
	// 数据库配置
	DBList DBListConf
}

type DBListConf struct {
	Mysql MysqlConf
}

type MysqlConf struct {
	Address     string
	Username    string
	Password    string
	DBName      string
	TablePrefix string
}

type ServiceContext struct {
	Config Config
	DBList *DBList
}

type DBList struct {
	Mysql *gorm.DB
}

func NewServiceContext(c Config) *ServiceContext {
	return &ServiceContext{
		Config: c,
		DBList: initDB(c),
	}
}

func initDB(c Config) *DBList {
	dbList := new(DBList)
	dbList.Mysql = initMysql(c)

	return dbList
}

func initMysql(c Config) *gorm.DB {
	dsn := fmt.Sprintf("%s:%s@tcp(%s)/%s?charset=utf8mb4&parseTime=True&loc=Local",
		c.DBList.Mysql.Username,
		c.DBList.Mysql.Password,
		c.DBList.Mysql.Address,
		c.DBList.Mysql.DBName,
	)

	db, err := gorm.Open(mysql.Open(dsn), &gorm.Config{
		NamingStrategy: schema.NamingStrategy{
			TablePrefix:   c.DBList.Mysql.TablePrefix, // 表名前缀
			SingularTable: true,                       // 使用单数表名
		},
		DisableForeignKeyConstraintWhenMigrating: true,
	})
	if err != nil {
		panic(err)
	}

	// 自动建表
	err = db.AutoMigrate(&model.Video{}, &model.Favorite{}, &model.Comment{})
	if err != nil {
		panic(err)
	}

	return db
}

func main() {
	// 创建配置实例
	config := Config{
		DBList: DBListConf{
			Mysql: MysqlConf{
				Address:     "Address",
				Username:    "root",
				Password:    "1111111111111",
				DBName:      "dbname",
				TablePrefix: "Prefix",
			},
		},
	}

	// 创建服务上下文
	serviceContext := NewServiceContext(config)
	
	// 在这里你可以使用 serviceContext 进行操作
	// ...

	fmt.Println("Database initialization and configuration completed.")
}


```

这里出现了自动建表的操作AutoMigrate 你需要把你想要创建的模型传进来自动建表


## CRUD

### 1. C
接下来开始 crud,

假如你有一个user表，可以使用create创建多个数据

```go
users := []*User{
    User{Name: "Jinzhu", Age: 18, Birthday: time.Now()},
    User{Name: "Jackson", Age: 19, Birthday: time.Now()},
}

result := db.Create(users) // pass a slice to insert multiple row

result.Error        // returns error
result.RowsAffected // returns inserted records count

```

Error相信你会接

```go
if result.Error != nil {
	return exit
}

```

或者可以直接在create里

```go
err := db.create().Error
if err != nil {
	return exit
}
```

### 2.R
根据主键检索

```go

db.First(&user, 10)
// SELECT * FROM users WHERE id = 10;

db.First(&user, "10")
// SELECT * FROM users WHERE id = 10;

db.Find(&users, []int{1,2,3})
// SELECT * FROM users WHERE id IN (1,2,3);

```
这里的first为检索第一个 limit为1

检索全部对象
```go
// Get all records
result := db.Find(&users)
// SELECT * FROM users;

result.RowsAffected // returns found records count, equals `len(users)`
result.Error        // returns error
```


条件检索

这样相当于拼sql语句

```go
// Get first matched record
db.Where("name = ?", "jinzhu").First(&user)
// SELECT * FROM users WHERE name = 'jinzhu' ORDER BY id LIMIT 1;

// Get all matched records
db.Where("name <> ?", "jinzhu").Find(&users)
// SELECT * FROM users WHERE name <> 'jinzhu';

// IN
db.Where("name IN ?", []string{"jinzhu", "jinzhu 2"}).Find(&users)
// SELECT * FROM users WHERE name IN ('jinzhu','jinzhu 2');

// LIKE
db.Where("name LIKE ?", "%jin%").Find(&users)
// SELECT * FROM users WHERE name LIKE '%jin%';

// AND
db.Where("name = ? AND age >= ?", "jinzhu", "22").Find(&users)
// SELECT * FROM users WHERE name = 'jinzhu' AND age >= 22;

// Time
db.Where("updated_at > ?", lastWeek).Find(&users)
// SELECT * FROM users WHERE updated_at > '2000-01-01 00:00:00';

// BETWEEN
db.Where("created_at BETWEEN ? AND ?", lastWeek, today).Find(&users)
// SELECT * FROM users WHERE created_at BETWEEN '2000-01-01 00:00:00' AND '2000-01-08 00:00:00';

```
高级查询 https://gorm.io/zh_CN/docs/advanced_query.html

### 3.U
更新 所有字段

```go
db.Save(&User{Name: "jinzhu", Age: 100})
// INSERT INTO `users` (`name`,`age`,`birthday`,`update_at`) VALUES ("jinzhu",100,"0000-00-00 00:00:00","0000-00-00 00:00:00")

db.Save(&User{ID: 1, Name: "jinzhu", Age: 100})
// UPDATE `users` SET `name`="jinzhu",`age`=100,`birthday`="0000-00-00 00:00:00",`update_at`="0000-00-00 00:00:00" WHERE `id` = 1

```
更多更新方法 https://gorm.io/zh_CN/docs/update.html


### 4.D

删除一条记录 

```go
// Email 的 ID 是 `10`
db.Delete(&email)
// DELETE from emails where id = 10;

// 带额外条件的删除
db.Where("name = ?", "jinzhu").Delete(&email)
// DELETE from emails where id = 10 AND name = "jinzhu";

```

更多删除方法 https://gorm.io/zh_CN/docs/delete.html

其中的软删除感觉很有用

> 当调用Delete时，GORM并不会从数据库中删除该记录，而是将该记录的DeleteAt设置为当前时间，而后的一般查询方法将无法查找到此条记录。
> 

## 总结

gorm是个很好用的go的orm框架，对很多数据库都兼容

性能也很好，操作起来也很简单。