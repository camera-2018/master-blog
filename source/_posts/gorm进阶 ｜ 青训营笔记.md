---
title: gorm 进阶 ｜ 青训营笔记
date: 2023-8-30 19:21:58
tags: 青训营笔记
---

## gorm

关于简单的 gorm 总结 可以看上一篇文章  gorm 初体验 ｜ 青训营笔记

### 软删除

`Gorm`提供了软删除的能力，需要在结构体中定义一个`Deleted`字段，此时再调用`Delete`删除函数，则会生成`update`语句，并将`deleted`字段赋值为当前删除时间。

```go
type Product struct {
    ID      uint
    Code    string
    Price   uint
    Deleted gorm.DeletedAt
}
```

再次执行删除操作：

```go
go
db.Delete(&Product{}, 1)
```

生成的`sql`为：

```sql
UPDATE `product` SET `deleted`='2023-01-30 22:22:22.202' WHERE `product`.`id` = 1 AND `product`.`deleted` IS NULL
```

查询被软删除的操作，要使用`Unscoped`函数：

```go
db.Unscoped().First(&product, 2)
```

有了`DeleteAt`字段后，删除操作已经变成了更新操作，那么想要物理删除怎么办？也是使用`Unscoped`函数：

```go
db.Unscoped().Delete(&Product{}, 1)
```

## Gorm事务

`Gorm`提供了`Begin`、`Commit`、`Rollback`方法用于使用事务。

```go
// 开启事务
tx := db.Begin()
if err := tx.Create(&Product{Code: "D32", Price: 100}).Error; err != nil {
    // 出现错误回滚
    tx.Rollback()
    return
}
if err := tx.Create(&Product{Code: "D33", Price: 100}).Error; err != nil {
    // 出现错误回滚
    tx.Rollback()
    return
}
// 提交事务
tx.Commit()
```

在开启事务后，调用增删改操作是应该使用开启事务返回的`tx`而不是`db`。

`Gorm`还提供了`Transaction`函数用于自定提交事务，避免用户漏写`Commit`、`Rollback`。

```go
if err = db.Transaction(func(tx *gorm.DB) error {
    if err := db.Create(&Product{Code: "D55", Price: 100}).Error; err != nil {
        return err
    }
    if err := db.Create(&Product{Code: "D56", Price: 100}).Error; err != nil {
        return err
    }
    return nil
}); err != nil {
    return
}
```

这种写法，当出现错误时会自动进行`Rollback`，当正常执行时会自动`Commit`。

### Hook

当我们想在执行增删改查操作前后做一些额外的操作时，可以使用`Gorm`提供的`Hook`能力。

`Hook`是在创建、查询、更新、删除等操作之前、之后自动调用的函数，如果任何`Hook`返回错误，`Gorm`将停止后续的操作并回滚事务。

`Hook`会开启默认事务，所以会带来了一些性能损失。

### 性能提高

对于写操作（创建、更新、删除），为了确保数据的完整性，`Gorm`会将他们封装在事务内运行，但是这样会降低性能，可以使用`SkipDefaultTransaction`关闭默认事务。

使用`PrepareStmt`缓存预编译语句可以提高后续调用的速度。

```go
db, err := gorm.Open(sqlite.Open("gorm.db"), &gorm.Config{
  SkipDefaultTransaction: true,
  PrepareStmt: true,
})
```

## Gorm生态

Gorm拥有非常丰富的扩展生态，下面列举一些常用的扩展。

- 代码生成工具:[github.com/go-gorm/gen](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fgo-gorm%2Fgen)
- 分片分库方案:[github.com/go-gorm/sha…](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fgo-gorm%2Fsharding)
- 手动索引:[github.com/go-gorm/hin…](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fgo-gorm%2Fhints)
- 乐观锁:[github.com/go-gorm/opt…](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fgo-gorm%2Foptimisticlock)
- 读写分离:[github.com/go-gorm/dbr…](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fgo-gorm%2Fdbresolver)
- OpenTelemetry扩展:[github.com/go-gorm/ope…](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fgo-gorm%2Fopentelemetry)

## GORM 配置

GORM 提供的配置可以在初始化时使用

```go
type Config struct {
  SkipDefaultTransaction   bool
  NamingStrategy           schema.Namer
  Logger                   logger.Interface
  NowFunc                  func() time.Time
  DryRun                   bool
  PrepareStmt              bool
  DisableNestedTransaction bool
  AllowGlobalUpdate        bool
  DisableAutomaticPing     bool
  DisableForeignKeyConstraintWhenMigrating bool
}
```

### 跳过默认事务

为了确保数据一致性，GORM 会在事务里执行写入操作（创建、更新、删除）。如果没有这方面的要求，您可以在初始化时禁用它。

```go
db, err := gorm.Open(sqlite.Open("gorm.db"), &gorm.Config{
  SkipDefaultTransaction: true,
})
```

### 命名策略

GORM 允许用户通过覆盖默认的`NamingStrategy`来更改命名约定，这需要实现接口 `Namer`

```go
type Namer interface {
    TableName(table string) string
    SchemaName(table string) string
    ColumnName(table, column string) string
    JoinTableName(table string) string
    RelationshipFKName(Relationship) string
    CheckerName(table, column string) string
    IndexName(table, column string) string
}
```

默认 `NamingStrategy` 也提供了几个选项，如：

```go
db, err := gorm.Open(sqlite.Open("gorm.db"), &gorm.Config{
  NamingStrategy: schema.NamingStrategy{
    TablePrefix: "t_",   // table name prefix, table for `User` would be `t_users`
    SingularTable: true, // use singular table name, table for `User` would be `user` with this option enabled
    NoLowerCase: true, // skip the snake_casing of names
    NameReplacer: strings.NewReplacer("CID", "Cid"), // use name replacer to change struct/field name before convert it to db name
  },
})
```

### NowFunc

更改创建时间使用的函数

```
db, err := gorm.Open(sqlite.Open("gorm.db"), &gorm.Config{
  NowFunc: func() time.Time {
    return time.Now().Local()
  },
})
```

### DryRun

生成 `SQL` 但不执行，可以用于准备或测试生成的 SQL

```
db, err := gorm.Open(sqlite.Open("gorm.db"), &gorm.Config{
  DryRun: false,
})
```

### PrepareStmt

`PreparedStmt` 在执行任何 SQL 时都会创建一个 prepared statement 并将其缓存，以提高后续的效率

```
db, err := gorm.Open(sqlite.Open("gorm.db"), &gorm.Config{
  PrepareStmt: false,
})
```

### 禁用嵌套事务

在一个事务中使用 `Transaction` 方法，GORM 会使用 `SavePoint(savedPointName)`，`RollbackTo(savedPointName)` 为你提供嵌套事务支持，你可以通过 `DisableNestedTransaction` 选项关闭它

### DisableAutomaticPing

在完成初始化后，GORM 会自动 ping 数据库以检查数据库的可用性，若要禁用该特性，可将其设置为 `true`

```
db, err := gorm.Open(sqlite.Open("gorm.db"), &gorm.Config{
  DisableAutomaticPing: true,
})
```

### DisableForeignKeyConstraintWhenMigrating

在 `AutoMigrate` 或 `CreateTable` 时，GORM 会自动创建外键约束，若要禁用该特性，可将其设置为 `true`

```
db, err := gorm.Open(sqlite.Open("gorm.db"), &gorm.Config{
  DisableForeignKeyConstraintWhenMigrating: true,
})
```
