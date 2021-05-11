# mysql基础

## 常用的SQL命令

- SHOW DATABASES; // 显示服务器上当前所有的数据库
- quit 退出连接;
- USE 数据库名; // 进入指定的数据库
- SHOW TABLES; // 显示当前数据库中所有的数据表
- DESC student; // 描述表中有哪些列(表头)

- SET NAMES UTF8;
- DROP DATABASE IF EXISTS cya;  // 丢弃指定的数据库 如果存在的话
- CREATE DATABASE cya ;         // 创建新的数据库
- USE cya ;                     // 进入创建的数据库
- CREATE TABLE student( sid INT, name VARCHAR(8) , sex VARCHAR(1), score INT);  // 创建表
- INSERT INFO  student VALUES('1', 'TOM', 'M', '99');              // 插入数据
- ...
- INSERT INFO  student VALUES('5', 'JOEY', 'M', '90');  
- SELECT * FROM cya;          // 查询
- UPDATE cya SET name="cya" WHERE uid='1'; //修改
- DELETE FROM cya WHERE uid='2'; // 删除

## 客户端 连接服务器 开启 mysql.exe

- Mysql.exe -h127.0.0.1 -P3306 -uroot -p   => Mysql -uroot

## 标准的sql语句分类

- DDL: Data Define Language  // 定义数据类型
  - CREATE/DROP/ALTER
- DML: Data Manipulate Language // 操作数据
  - INSERT/UPDATE/DELETE
- DQL: Data Query Language // 查询数据
  - SELECT
- DCL: Data Control Language // 控制用户权限
  - GRANT(授权)/REVOKE(收权)

## 解决sql文件中文乱码问题

- 脚本另存为 -> 选择utf-8 -> 在设置里面设置文件默认编码为utf-8
- 客户端连接服务器端 -> 在脚本第一行 SET NAMES UTF8
- 服务器端 创建数据库的同时,要设置存储数据的编码为utf8 => - CREATE DATABASE cya CHARSET=UTF8;

## 列类型

- 数值型
  - tinyInt 微整型 占1个字节(8位) 范围 -128~127
  - smallInt 小整型 占2个字节 范围 -32768~32767
  - int 整型 占4个字节 范围 -2147483648~2147483647
  - bigInt 大整型 占8个字节 范围 很大
  - float 单精度浮点型 占4个字节 范围 最多 3.4E38 可能产生误差
  - double 双精度浮点型 占8个字节 范围 比bigInt 大的多 可能产生误差
  - decimal(M,D) 定点小数 不会产生计算误差 m代表总的有效位数(不包含小数点), d代表小数点后的有效位数
  - bool 布尔型 true, false => 真正存储都时候是 tinyint型 变成了 1和0 => 也可以直接插入1和0
- 日期时间型
  - DATE 日期型 '2021-4-17'
  - TIME 时间型 '11:17:30'
  - DATETIME 日期时间型 ''2021-4-17 11:17:30'
- 字符串类型
  - VARCHAR(M) 变长字符串 不会产生空间浪费 m 的最大值是 65535
  - CHAR(M) 定长字符串 => 必须要占满 M 字符 可能产生空间浪费,但操作速度比较快 m的最大值 255 => 身份证 手机号
  - TEXT(M) 大型变成字符串 M 最大 2G
- 列约束
  - 在插入数据的时候,进行特定的验证; 只有满足条件才允许插入,否则被认为是非法插入
  - create table cya ( eid 列类型 列约束,)
    - 主键约束 PRIMARY KEY (primary key)
      - 声明了主键约束上的值不能出现重复,一个表里面只能有一个主键
      - 声明了主键约束后就不允许插入null值
    - 非空约束
      - NOT NULL
      - create table cya ( eid 列类型 列约束, name 列类型 NOT NULL)
    - 唯一约束
      - UNIQUR (uniqur)
    - 默认值约束
      - DEFAULT 3000
      - 可以使用关键字 default 声明默认值
      - INSERT INTO cya(eid, price) VALUES(2, 3000); => 其他所有的列都使用默认值
    - 检查约束(mysql不支持: 认为会降低数据的插入速度)
      - CHECK
      - 可以对插入的数据进行自定义的验证
      - score TINYINT CHECK (score >=0 and score <= 100)
    - 外键约束
      - FOREIGN KEY => foreign(开发时大写) 另外一个表的主键, 列类型也要一致, 允许使用NULL的
      - 在创建的时候,另起一行 FOREIGN KEY (familyID) REFERENCES '表名'(fid)
- 自增列
  - AUTO_INCREMENT : 自动增长,假如一个列声明了自增列,无需手动赋值(创建的时候填入null),默认取最大值+1
  - create table cya ( eid 列类型 列约束 AUTO_INCREMENT,)
  - 只适用于整数型的主键列, 允许手动赋值

- 简单查询
  - 查询所有的列
    - select * from cya
  - 查询特定的列
    - select name,sex from cya
  - 给列起别名
    - select name as 姓名, sex as 性别 from cya
  - 显示不同的记录/合并相同的项
    - select DISTINCT sex From cya
  - 查询时执行计算
    - select name, salary*12 as 年薪 from cya
  - 对查询出来的结果集排序
    - select * from cya order by eid ASC  // ascendant 升序  简写 asc descendent 降序 简写 desc
    - order by 可以按照数值,日期时间,字符串排序, 默认是升序排列 也可以多组排序规则
    - select * from cya order by sex ASC, name desc
  - 条件查询
    - select * from cya where eid=5;
    - select * from cya where type='开发部' order by eid;
    - >< 不等于 and 并且 or 或者 in 满足之一就行
    - select * from cya where type IS NULL; // 查询部门没有明确的(null)
    - select * from cya where type IS NOT NULL; // 查询部门有明确的(除了 null)
    - select * from cya where salary >= 6000 and salary <= 8000;
    - 上面另一种写法 select * from cya where salary BETWEEN 6000 AND 8000;
    - select * from cya where salary < 6000 OR salary > 8000;
    - 上面另一种写法 select * from cya where salary NOT BETWEEN 6000 AND 8000;
    - select * from cya where eid in(1,2);
    - select * from cya where eid not in(1,2);
  - 模糊条件查询 % 代表0个或多个 _: 代表一个站位 %c_ 代表查询倒数第二个字符为c 的数据 %c__ :两个代表 倒数第三个字符
    - select * from cya where name lick '%c%'
  - 分页查询 limit : start 开始的值, count 每页的数据量
    - 需要有两个条件 : 当前的页码, 每页的数据量 => start =(页码-1) * count
    - select * from cya LIMIT start, count;
    - 第一页: select * from cya LIMIT 0,5
    - 第二页: select * from cya LIMIT 5,5
    - 第三页: select * from cya LIMIT 10,5
    - 第四页: select * from cya LIMIT 15,5
    - 第五页: select * from cya LIMIT 20,5
- 复查查询 count: 总数量/sum: 总和/avg: 平均值/max/min
  - 聚合查询/分组查询
  - select COUNT(eid) from cya;
  - select COUNT(*) from cya; // 推荐写法
  - 分组查询只能查询分组条件和聚合函数
    - select sex, Max(sakary) from cya group by sex;
    - select year('1996-8-6'); => 1996 : year() 是获取年份的函数 month() 获取日期中的月份
  - 子查询
    - select * from emp where type=(select * from cya where type='开发部');
  - 多表查询
    - 内连接 inner join
      - select name, dname from emp INNER JOIN cya ON Did = eid;
    - 左外连接 left outer join  => outer关键字是可以省略的
      - select name, dname from emp LEFT OUTER JOIN cya ON Did = eid;
    - 右外连接 显示右侧表中所有的记录 right => outer关键字是可以省略的
      - select name, dname from emp RIGHT OUTER JOIN cya ON Did = eid;
    - 全连接 左右都显示 full join on  (mysql不支持)
      - union 合并的意思 unionall 不合并的意思

- 增删改查总结

```javascript
// 开启mysql 
mysql -uroot
INSERT INTO MesWoWorder VALUES(...)
DELETE FROM MesWoWorder WHERE id = 5;
UPDATE MesWoWorder SET upwd='1234',sex=0 WHERE id = 4;
SELECT * FROM MesWoWorder
```
