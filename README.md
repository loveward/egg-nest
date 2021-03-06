## EGG-NEST

egg-nest是学习postgrest的逻辑和模式，以纯js实现的后端Restful-Api自动生成框架 用于快速生成项目后端，实现非常简易的项目效果演示等用途

#### （不建议用在生产环境，用golang的女朋友框架是更好更完整的选择）

egg-nest基于egg开发，代码很简单，进一步开发也很方便
（比如产品经理设想了一个功能，数据结构一设计，直接拿egg-nest一套，一个完整的功能演示就出现了，可能半小时就能看到想要的效果，然后进一步扩宽产品和业务设计的思路和）

已经设置上传限制大小为2M,可以在/config/config.default.js里面修改。

## 使用
安装了yarn之后
```bash
yarn
# 安装依赖
yarn start
#启动服务
yarn dev
#开发者模式
#更多命令参照package.json
```

## docker中使用
```bash
docker run --name egg-nest -p 7001:7001 -v /your/config:/eggnest/sql -d loveward/egg-nest:latest
# config的写法在下面有 ↓
```

目前已经实现的功能：
+ 多数据库配置管理
    在sql/sqlconfig.json里面写对应的配置，可以有多个配置
```json
{
    "sql":{
        "host":"pgsql.egg-nest.com",
        "port":65432,
        "user":"egg-nest",
        "password":"123456egg-nest",
        "database":"egg-nest"
    }
}
```

多个数据库配置可以：
```json
{
    "sql":{
        "host":"pgsql.egg-nest.com",
        "port":65432,
        "user":"egg-nest",
        "password":"123456egg-nest",
        "database":"test"
    },
    "dev":{
        "host":"pgsql.egg-nest.com",
        "port":65432,
        "user":"egg-nest",
        "password":"123456egg-nest",
        "database":"test"
    }
}
```

+ 多数据库健康检查

    健康检查的开关在：app\schedule\update_cache.js:22 

    

+ 多数据库单表CURD，说明如下：



## 查：

GET /api/v1/database_config/schema_name/table_name

参数列表

| 名称      | 描述                                   | 栗子                                                         | sql                                                          |
| --------- | -------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| aggregate | (选填)select某个字段，不写就是select * | /api/v1/dev/public/order?aggregate=description               | SELECT description FROM schema_name.table_name               |
| where     | (选填)字段$=.值                        | /api/v1/dev/public/order?where=description$=.'宫保鸡丁配⽶饭' | SELECT * FROM schema_name.table_name WHERE description='宫保鸡丁配⽶饭' |
| orderby   | (选填)字段                             | /api/v1/dev/public/order?orderby=description                 | SELECT * FROM schema_name.table_name ORDER BY description    |
| limit     | (选填)数字                             | /api/v1/dev/public/order?limit=1                             | SELECT * FROM schema_name.table_name LIMIT 1                 |
| offset    | (选填)数字                             | /api/v1/dev/public/order?offset=1                            | SELECT * FROM schema_name.table_name OFFSET 1                |

eg:  
    GET http://ip:port/api/v1/database_config/schema_name/table_name?where=description$=.'宫保鸡丁配⽶饭'&orderby=description&limit=2&offset=1

    return：
    [
        {"orderid":233,"subject": "外卖餐品","description": "宫保鸡丁配⽶饭"},
        {"orderid":234,"subject": "外卖餐品","description": "宫保鸡丁配⽶饭"}
    ]

## 增：

POST /api/v1/database_config/schema_name/table_name

参数列表


| 名称 | 描述                             | 栗子                                                         | sql                                                          |
| ---- | -------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 字段 | 值(json 或 x-www-from-urlencoded) | {"orderid":233,"subject": "外卖餐品","description": "宫保鸡丁配⽶饭"} | INSERT INTO schema_name.table_name (orderid, subject, description) VALUES ('233', '外卖餐品', '宫保鸡丁配⽶饭') |

eg:
    POST http://ip:port/api/v1/database_config/schema_name/table_name
    
    body:
    {"orderid":234,"subject": "外卖餐品","description": "宫保鸡丁配⽶饭"}

    return:

## 删：

DEL /api/v1/database_config/schema_name/table_name

参数列表


| 名称  | 描述            | 栗子                                                         | sql                                                          |
| ----- | --------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| where | (必填)字段$=.值 | /api/v1/dev/public/order?where=description$=.'宫保鸡丁配⽶饭' | DELETE FROM schema_name.table_name WHERE description='宫保鸡丁配⽶饭' |



## 改：
PUT /api/v1/database_config/schema_name/table_name

参数列表

| 名称  | 描述            | 栗子                                                    | sql                                                          |
| ----- | --------------- | ------------------------------------------------------- | ------------------------------------------------------------ |
| where | (必填)字段$=.值 | /api/v1/dev/public/order?where=orderid$=.'233'          |                                                              |
| set   | (必填)字段$=.值 | /api/v1/dev/public/order?where=description$=.'鸡腿配面' | UPDATE schema_name.table_name SET description='鸡腿配面' WHERE orderid='233' |



计划中将要实现的功能：
+ 数据校验???
+ 联表查询???
+ 跨库联表查询???
+ 动态建表???
+ 动态增删字段???
+ 等等。。。

#### 等待更多有价值的意见中ing
