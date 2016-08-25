# Database
# 数据库

<!-- toc -->

## Connecting to a database
## 连接到数据库

In order to connect to a database, you need to import the database's driver first. For example:
为了能连接到数据库，你需要先导入数据库驱动，比如：

```go
import _ "github.com/go-sql-driver/mysql"
```

GORM has wrapped some drivers, for easier to remember the import path, so you could import the mysql driver with

```go
import _ "github.com/jinzhu/gorm/dialects/mysql"
// import _ "github.com/jinzhu/gorm/dialects/postgres"
// import _ "github.com/jinzhu/gorm/dialects/sqlite"
// import _ "github.com/jinzhu/gorm/dialects/mssql"
```

#### MySQL

**NOTE:** In order to handle `time.Time`, you need to include `parseTime` as a parameter. ([More supported parameters](https://github.com/go-sql-driver/mysql#parameters))

```go
import (
    "github.com/jinzhu/gorm"
    _ "github.com/jinzhu/gorm/dialects/mysql"
)

func main() {
  db, err := gorm.Open("mysql", "user:password@/dbname?charset=utf8&parseTime=True&loc=Local")
  defer db.Close()
}
```

#### PostgreSQL

```go
import (
    "github.com/jinzhu/gorm"
    _ "github.com/jinzhu/gorm/dialects/postgres"
)

func main() {
  db, err := gorm.Open("postgres", "host=myhost user=gorm dbname=gorm sslmode=disable password=mypassword")
  defer db.Close()
}
```

#### Sqlite3

```go
import (
    "github.com/jinzhu/gorm"
    _ "github.com/jinzhu/gorm/dialects/sqlite"
)

func main() {
  db, err := gorm.Open("sqlite3", "/tmp/gorm.db")
  defer db.Close()
}
```

#### Write Dialect for unsupported databases
#### 为不被支持的数据库编写“方言”

GORM officially supports the above databases, but you could write a dialect for unsupported databases.
GORM 官方仅支持以上几种数据库，但你可以通过编写“方言”以支持更多的数据库。

To write your own dialect, refer to: [https://github.com/jinzhu/gorm/blob/master/dialect.go](https://github.com/jinzhu/gorm/blob/master/dialect.go)
要想编写你自己的“方言”，可以参考:

## Migration
## 迁移

### Auto Migration
### 自动迁移

Automatically migrate your schema, to keep your schema update to date.
自动迁移你的表模式，以使你的表模式更新到最新状态。

**WARNING:** AutoMigrate will **ONLY** create tables, missing columns and missing indexes, and **WON'T** change existing column's type or delete unused columns to protect your data.
**警告:** 自动迁移将 **仅仅** 创建新表，缺失的列以及缺失的索引，并且 **不会** 修改已经存在列的类型，或者删除那些没有使用的列以保护你的数据。

```go
db.AutoMigrate(&User{})

db.AutoMigrate(&User{}, &Product{}, &Order{})

// Add table suffix when create tables
db.Set("gorm:table_options", "ENGINE=InnoDB").AutoMigrate(&User{})
```

### Has Table
### 表已经存在

```go
// Check model `User`'s table exists or not
db.HasTable(&User{})

// Check table `users` exists or not
db.HasTable("users")
```

### Create Table
### 创建表

```go
// Create table for model `User`
db.CreateTable(&User{})

// will append "ENGINE=InnoDB" to the SQL statement when creating table `users`
db.Set("gorm:table_options", "ENGINE=InnoDB").CreateTable(&User{})
```

### Drop table
### 删除表

```go
// Drop model `User`'s table
db.DropTable(&User{})

// Drop table `users
db.DropTable("users")

// Drop model's `User`'s table and table `products`
db.DropTableIfExists(&User{}, "products")
```

### ModifyColumn
### 修改列

Modify column's type to given value
将列的类型修改为指定类型

```go
// change column description's data type to `text` for model `User`
db.Model(&User{}).ModifyColumn("description", "text")
```

### DropColumn
### 删除列

```go
// Drop column description from model `User`
db.Model(&User{}).DropColumn("description")
```

### Add Foreign Key

```go
// Add foreign key
// 1st param : foreignkey field
// 2nd param : destination table(id)
// 3rd param : ONDELETE
// 4th param : ONUPDATE
db.Model(&User{}).AddForeignKey("city_id", "cities(id)", "RESTRICT", "RESTRICT")
```

### Indexes
### 索引

```go
// Add index for columns `name` with given name `idx_user_name`
db.Model(&User{}).AddIndex("idx_user_name", "name")

// Add index for columns `name`, `age` with given name `idx_user_name_age`
db.Model(&User{}).AddIndex("idx_user_name_age", "name", "age")

// Add unique index
db.Model(&User{}).AddUniqueIndex("idx_user_name", "name")

// Add unique index for multiple columns
db.Model(&User{}).AddUniqueIndex("idx_user_name_age", "name", "age")

// Remove index
db.Model(&User{}).RemoveIndex("idx_user_name")
```
