# db-migrate

参照Laravel开发的easyswoole数据库版本迁移工具

## 使用方法

在全局 `boostrap` 事件中注册 `MigrateCommand` 并添加配置信息

> bootstrap.php

```php
\EasySwoole\Command\CommandManager::getInstance()->addCommand(new \EasySwoole\DatabaseMigrate\MigrateCommand());
$config = new \EasySwoole\DatabaseMigrate\Config\Config();
$config->setHost("127.0.0.1");
$config->setPort(3306);
$config->setUser("root");
$config->setPassword("123456");
$config->setDatabase("easyswoole");
$config->setTimeout(5.0);
$config->setCharset("utf8mb4");
//===========可选配置修改项，以下参数均有默认值===========
// 迁移记录的数据库表名
$config->setMigrateTable("migrations");
// 迁移文件目录的绝对路径
$config->setMigratePath(EASYSWOOLE_ROOT . '/Database/Migrates/');
// 迁移模板文件的绝对路径
$config->setMigrateTemplate(EASYSWOOLE_ROOT . '/vendor/easyswoole/db-migrate/src/Resource/migrate._php');
// 迁移模板类的类名
$config->setMigrateTemplateClassName("MigratorClassName");
// 迁移模板类的表名
$config->setMigrateTemplateTableName("MigratorTableName");
// 迁移模板创建表的模板文件的绝对路径
$config->setMigrateCreateTemplate(EASYSWOOLE_ROOT . '/vendor/easyswoole/db-migrate/src/Resource/migrate_create._php');
// 迁移模板修改表的模板文件的绝对路径
$config->setMigrateAlterTemplate(EASYSWOOLE_ROOT . '/vendor/easyswoole/db-migrate/src/Resource/migrate_alter._php');
// 迁移模板删除表的模板文件的绝对路径
$config->setMigrateDropTemplate(EASYSWOOLE_ROOT . '/vendor/easyswoole/db-migrate/src/Resource/migrate_drop._php');
// 数据填充目录绝对路径
$config->setSeederPath(EASYSWOOLE_ROOT . '/Database/Seeds/');
// 数据填充模板类的类名
$config->setSeederTemplateClassName("SeederClassName");
// 数据填充模板文件的绝对路径
$config->setSeederTemplate(EASYSWOOLE_ROOT . '/vendor/easyswoole/db-migrate/src/Resource/seeder._php');
// 逆向生成迁移文件的模板文件绝对路径
$config->setMigrateGenerateTemplate(EASYSWOOLE_ROOT . '/vendor/easyswoole/db-migrate/src/Resource/migrate_generate._php');
// 逆向生成迁移模板SQL语句的DDL代码块
$config->setMigrateTemplateDdlSyntax("DDLSyntax");
\EasySwoole\DatabaseMigrate\MigrateManager::getInstance($config);
```

执行 `php easyswoole migrate -h`

```text
php easyswoole migrate -h
Database migrate tool

Usage:
  easyswoole migrate ACTION [--opts ...]

Actions:
  create    Create the migration repository
  generate  Generate migration repository for existing tables
  run       run all migrations
  rollback  Rollback the last database migration
  reset     Rollback all database migrations
  seed      Data filling tool
  status    Show the status of each migration

Options:
  -h, --help  Get help
```

> `create`  

创建一个迁移模板

可用操作选项：

- `--alter`：创建一个修改表的迁移模板
  - 示例：`php easyswoole migrate create --alter=TableName`
- `--create`：创建一个建表的迁移模板
  - 示例：`php easyswoole migrate create --create=TableName`
- `--drop`：创建一个删表的迁移模板
  - 示例：`php easyswoole migrate create --drop=TableName`
- `--table`：创建一个基础迁移模板
  - 示例：`php easyswoole migrate create --table=TableName`  等同于 `php easyswoole migrate create TableName`

> `generate` 

对已存在的表生成适配当前迁移工具的迁移模板

可用操作选项：

- `--tables`：指定要生成迁移模板的表，多个表用 ',' 隔开
  - 示例：`php easyswoole migrate generate --tables=table1,table2`
- `--ignore`：指定要忽略生成迁移模板的表，多个表用 ',' 隔开
  - 示例：`php easyswoole migrate generate --ignore=table1,table2`

> run

迁移所有未迁移过的版本

> rollback

回滚迁移记录，默认回滚上一次的迁移，指定操作相关参数可以从status命令中查看

可用操作选项：

- `--batch`：指定要回滚的批次号 
  - 示例：`php easyswoole migrate rollback --batch=2`
- `--id`：指定要回滚的迁移ID
  - 示例：`php easyswoole migrate rollback --id=2`

> reset

回滚所有迁移记录

> seed

数据填充工具

可用操作选项：

- `--class`：指定要填充的class name，也就是文件名 ==（请保证填充工具文件名与类名完全相同）== 
  - 示例：`php easyswoole migrate seed --class=UserTable`
- `--create`：创建一个数据填充模板
  - 示例：`php easyswoole migrate seed --create=UserTable`

> status

迁移状态
