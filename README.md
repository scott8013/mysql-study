# mysql-study
### mysql安装

### 数据库启动 停止
```
启动MySQL服务
sudo /usr/local/MySQL/support-files/mysql.server start

停止MySQL服务
sudo /usr/local/mysql/support-files/mysql.server stop

重启MySQL服务
sudo /usr/local/mysql/support-files/mysql.server restart

// .zshrc文件中
alias mysql=/usr/local/mysql/bin/mysql
alias mysqladmin=/usr/local/mysql/bin/mysqladmin

mysql -V
mysql --version
```

### mysql 服务的登录和退出
```
mysql [-h 127.0.0.1 -P 3306] -u root -p  // 直接回车 敲密码 或者直接在 -p后加密码
exit // 退出
```
### mysql的常用命令
```
show dadabase; // 当前数据库的表

use test; // 使用test数据库 

show tables; // 显示当前数据库下的表

show tables from mysql;  // 查表

select database();  查询当前使用的是哪个数据库

// 创建一个只有name id的表
create table stuinfo(
  id int,
  name varchar(20)
);
show tables; // 查看当前数据库下的表

desc stuinfo;  //查看表结构 会显示表的信息

select * from stuinfo; // 检索stuinfo 表中的数据
// 插入id =1 name = 'john'的一条数据
insert into stuinfo (id, name) values (1, 'john');

insert into stuinfo (id, name) values (2,  'rose');

insert into stuinfo (id, name) values (3,  'rose');
// 查询stuinfo下的所有数据
select * from stuinfo;
// 更新id = 1 的那条数据的name为李磊
update stuinfo set name='lilei' where id = 1;
// 从stuinfo中删除 id = 1的那条数据
delete from stuinfo where id =1;

// 查看数据库版本
select version();
```
## SQL语言
### DQL Data Query Language 针对查询
#### 进阶1. 基础查询
> 查询列表可以是, 表中的字段 常亮值, 表达式 函数.
```
use myemployees; // 记住加上这句 使用哪个库
select 查询列表 from 表名;
SELECT last_name FORM employees; // 查询单个字段
SELECT last_name, salary, email FORM employees; // 查询多个字段 用',' 分割
SELECT * FROM employees; // 查询所有字段
```
// 常量值
```
SELECT 100;
SELECT 'john'
```

// 表达式

```
SELECT 100 * 98;
```
// 函数
```
SELECT VERSION();
```

// 起别名
```
SELECT 100 * 98 AS 结果 // 使用AS关键字起别名
SELECT last_name As 姓, first_name AS 名 FORM employees;
SELECT last_name  姓, first_name  名 FORM employees; // 用空格
SELECT salary AS "out_put" FROM employees;
```
// 去重 DISTINCT
```
SELECT DISTINCT department_id FORM employees;
```
// +号的作用

>  '+' 在sql语句中只能做运算符, 1. 全是数字,相加 2. 一方为数字 一方为字符串 试图将字符串转为数值型 如果转换成功, 继续做加法运算 3.如果转换失败, 会将字符串转换为0 再做加法运算 4. 其中一个为null, 结果一定为null

```
  SELECT 10 + 20; // => 30;
  SELECT 10 + '20' // => 30
  SELECT 'john' + 20 // => 20
  SELECT null + 90 // => null
  SELECT last_name + first_name AS 姓名 from employees; // => 0
  SELECT CANCAT(last_name, first_name) AS 姓名 from employees
  SELECT CANCAT(last_name, first_name, IFNULL(commission_pct, 0) ) AS 姓名 from employees; // IFNULL 函数使用
```
#### 条件查询

> 语法: SELECT 查询列表 FROM 表名 WHERE 筛选条件
> - 1.按条件表达式筛选
> 条件运算符 > < = != <> >= <=

```
SELECT * FROM employees WHERE salary > 12000;
SELECT * last_name, depeartment_id FROM  employees WHERE department_id<>90; 
```

> - 2.按条件运算符查询
> &&(并且 和 and相同效果) ||(或 和 or 相同) !(取反 和 not 相同) not or and

```
SELECT last_name, salary, commission_pct FROM employees WHEER salary > 20000  && salary < 10000;
SELECT * FROM employees department_id > 20000  && salary < 10000;
SELECT * FROM employees WHERE !(department_id > 90 and department_id < 120) || salary > 10000;
```
> - 3.模糊查询
> like,  between... and... in , is null

> 一般和通配符搭配使用 % 任意多个字符 [0, ∞)

> '_' 任意单个字符

```
SELECT * FROM employees WHERE last_name like '%a%'; // 搜索last_name中包含 'a'
SELECT * FROM employees WHERE like '__n_l%' // 第三个为n 第5个为l的
SELECT * FROM employees WHERE like '_\_%' // 第二个字符是_的
SELECT * FROM employees WHERE like '_$_%' ESCAPE '$' // 第二个字符是_的 指定$为通配符
SELECT * FROM employees WHERE employee_id between 100 and 120; // 1.包含临界值
SELECT * FROM employees WHERE employee_id >= 100 and  employee_id <= 120;
SELECT last_name FROM employees WHERE job_id = 'IT_PROG' OR job_id = 'AG_PV' OR job_id = 'AG_PROC';
SELECT last_name FROM employees WHERE job_id IN('IT_PROG','AG_VP', AG_PROC); // 1.in关键字 判断某字段的值 是否在IN列表中 2.提高语句简洁度
SELECT * FROM employees WHERE commission_pct IS NULL; // IS NOT NULL tips: = <> 不能判断NULL值
// 安全等于可以判断NULL值 和 普通值 <=>
SLEECT * FROM employees WHERE commission_pct <=> NULL;
SLEECT * FROM employees WHERE salary <=> 12000;
```
> 练习:

```
  SELECT 
    salary, last_name
  FORM
  employees 
  WHERE 
   commission_pct is null 
   and salary < 18000;
   
     SELECT 
    *
  FORM
    employees 
  WHERE 
   job_id <> 'IT' 
   or salary=12000;
   
   desc department;
 
  SELECT distinct location_id FROM department;
```

### 排序 order by
> 进阶3: 排序查询
```
SELECT * FROM employees order by salary desc; // 降序
SELECT * FROM employees order by salary asc; // 升序  不写 asc 默认是升序
SELECT * FROM employees WHERE department_id >= 90 ORDER BY hiredate ASC; 
SELECT  *, salary * 12 * (1 + IFNULL(commission_pct, 0)) AS total_salary FORM employees ORDER BY total_salary DESC;
SELECT  *, salary * 12 * (1 + IFNULL(commission_pct, 0)) AS total_salary FORM employees ORDER BY  salary * 12 * (1 + IFNULL(commission_pct, 0)) DESC;
SELECT last_name, salary FROM employees ORDER BY LENGTH(last_name);
SELECT * FORM employees ORDER BY salary ASC,  employees_id DESC; 

SHOW VARIABLES LIKE '%char%'
// 字符集是utf-8 一个汉字占三个字节 jbk字符集占 2个字节
```
### 常见函数的学习
> 进阶4 常见函数 - 字符函数
- length()
- cancat()
- upper() 
- lower()
- supstr()  substring() // 截取字符串
- instr() // 返回其实索引
- trim() 
- lpad()
- rpad()
- replace()
> 数学函数
- round() // 四舍五入
- ceil // 向上取整
- floor // 向下取整
- truncate() 截取小数位数
- mod() // 取余
> 日期函数
- now()
- curdate()
- curtime()
- year()
- month()
- str_to_date()
- data_formate()
> 其他函数
 - version()
 - database()
 - user()
> 流程控制函数
- if()
```
SELECT IF(10>5, '大', '小') //  => 大
SELECT IF(10<5, '大', '小') //  => 小
SELECT last_name, commission_pct, IF(commission_pct IS NULL, '没奖金, 呵呵', '有奖金, 嘻嘻') AS 备注;
```
 
#### DML 数据操纵语言 (Data manipulation language) 增删改
#### DDL Data Define Language
#### TCL (Transaction Control Language) 事务控制语言
