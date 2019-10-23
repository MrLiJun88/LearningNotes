# MySQL学习

## 1.关闭开启MySQL服务

```powershell
#启动
net start mysql
#停止
net stop mysql
```

## 2.MySQL登录

```powershell
mysql -h 主机名 -P 端口号 -u 用户名 -p 密码
#如果是本机登录 
mysql -u 用户名 -p 密码
#退出
exit
```

## 3.MySQL常用命令

```powershell
#查看当前在哪个数据库中
select database();
#查看其他库中有哪些表
show tables from 库名称
#查看当前数据库版
select version();
#查看当前所有的数据库
show databases
```

## 4.MySQL注释

```powershell
# 注释内容
-- 注释内容  注意：--后必须要有空格
/* 注释内容 */
```

## 5.DQL语句

### 5.1 基础查询

````mysql
#添加别名,两种方式，as与空格
select gender as '姓别' from xxx
select gener '姓名' from xxx

#去重复数 distinct
SELECT DISTINCT department_id FROM employees ;

#在sql中 + 的作用：仅仅只有一个功能，运算符，而非字符串的拼接
select 100+90; 两个操作数都为数值型，则做加法运算
select 'lijun'+90 结果为90，如果字符转换成功，则做运算，失败，则字符型数值为0
select null+90 结果为null，只要其中有一方为null，则结果肯定为null

#字符拼接 select concat(...)函数
SELECT CONCAT(last_name,first_name,...) AS 'Name' FROM employees;

#如果name为null返回1，否则返回0
select isnull(name)

#判断字段值是否为 null ,为null,则取后面的值
select ifnull(salary,0)
SELECT CONCAT(last_name,IFNULL(commission_pct,0)) AS 'Name' FROM employees;
````

### 5.2 条件查询

> 1.按条件表达式筛选( > , <  , = , != , <> , >= , <= )
>
> 2.按逻辑表达式筛选(and ,or ,not)
>
> 3.模糊查询(like ,between and ,in ,is null ,is not null )
>
> 4.安全等于  <=>
>
> ​    is null :仅仅可以判断 null 值
>
> ​    <=> : 即可以判断null值，也可以判断普通数值

```mysql
#查询名字中带有 a 的员工信息  通配符 %：代表任何多个字符,包含多个
#                               _:代表一个字符
SELECT * FROM employees WHERE last_name LIKE '%a%'

#查询员工姓名第二个字符是下划线的员工信息  '\_' 转义为普通下划线
SELECT last_name,salary FROM employees WHERE last_name LIKE '_\_%'
#也可以定义转义字符，使用 ESCAPE 关键字，指定后面的字符为转义字符
SELECT last_name,salary FROM employees WHERE last_name LIKE '_@_%' ESCAPE '@'

#使用 between and 查询员工id在100到120的员工信息 (前后包含匹配)
SELECT * FROM employees
WHERE
employee_id BETWEEN 100 AND 120 

#使用 in 判断某字段的值是否属性in(...),中的哪一个，符合条件的列出来,不支持通配符
SELECT 
  last_name,
  job_id 
FROM
  employees 
WHERE job_id IN ('IT_PROT', 'AD_VP', 'AD_PRES') ;

#查询奖金不为空的员工
SELECT 
  last_name,
  commission_pct 
FROM
  employees 
WHERE commission_pct IS NOT NULL 
```

````mysql
#经典面试题
select * from employees 和 
select * from employees where commission_pct like '%%' and last_name like '%%';
结果不一样？
答：如果判断的字段没有null值一样，有null值就会不一样
````

### 5.3排序查询

```mysql
#按照工资降序排序 升序 ASC	
SELECT 
  * 
FROM
  employees 
ORDER BY salary DESC ;

#按照员工年薪降序显示员工信息
SELECT 
  *,
  salary * 12*(1+IFNULL(commission_pct,0)) AS '年薪' 
FROM
  employees 
ORDER BY '年薪' DESC ;

#按姓名的长度显示员工的姓名和工资(得到字符串长度函数 length() )
SELECT 
last_name,salary
FROM 
employees
ORDER BY LENGTH(last_name) DESC

#查询员工信息先按工资升序，再按员工编号降序显示
/*
order by 子句中可以支持单个字段，多个字段，表达式，函数，别名
order by 子句一般是放在查询语句的最后面，limit子句除外
*/
SELECT 
*
FROM employees
ORDER BY salary ASC,employee_id DESC;
```

````mysql
#查询员工的姓名和部门号和年薪，按年薪降序，按姓名升序
SELECT 
  last_name,
  department_id,
  salary * 12 * (1+ IFNULL(commission_pct, 0)) AS '年薪' 
FROM
  employees 
ORDER BY '年薪' DESC,
  last_name ASC ;

#选择工资不在8000到17000的员工的姓名和工资，按工资降序
SELECT 
  last_name,
  salary 
FROM
  employees 
WHERE NOT salary BETWEEN 8000 
  AND 17000 
ORDER BY salary DESC ;

#查询邮箱中包含e的员工信息，并先按邮箱的字节数降序，再按部门号升序
SELECT 
  * 
FROM
  employees 
WHERE email LIKE '%e%' 
ORDER BY LENGTH(email) DESC,
  department_id ASC ;
````

### 5.4 单行函数的使用

>函数的调用：select 函数名(实参列表)
>
>分类：单行函数( concat() , length() , ifnull() )
>
>​           分组函数 (统计函数，组函数)

#### 5.4.1 字符函数

>字符函数，数学函数，日期函数，其他函数，流程控制函数 

```mysql
/* 
 length(str) 获取字符串长度
 concat(...) 拼接字符串
 upper(str),lower(str) 将字符串转成大写或者小写
 substr() 或 substring() 截取字符串
 instr('父串','子串') 返回子串出去在父串中的第一次出现的位置下标，如果找不到返回 0
 trim('  str     ') 去字符串左右的空格 
 trim('str1' from 'str2') 将str2前后左右的str1字符串去掉
       如： SELECT TRIM('a'FROM 'aaaaaa李俊aaaaa');  结果 李俊
 lpad('str1',length,'str2') 用指定的字符实现左填充至长度
       如：SELECT LPAD('lijun',10,'**'); 结果：*****lijun
 rpad('str1',length,'str2') 用指定的字符实现右填充至长度
       如：SELECT RPAD('lijun',10,'**');  结果：lijun*****
 replace('str1','replaced','replace') 用第三个参数来替换str1中的所在第二个参数
       如：SELECT REPLACE('lijun love wmy','wmy','lulu'); 结果：lijun love lulu
*/
#从 i 开始，截取后面所有的字符串
SELECT SUBSTRING('lijunlovewmy',i);
#截取从i开始，截取长度为 length 的字符串
SELECT SUBSTR('lijunlovewmy',i,length);

#姓名中首字母大写，其他字符小写，然后用 '_' 拼接，显示出来 
SELECT 
  CONCAT(
    UPPER(SUBSTRING(last_name, 1, 1)),
    '_',
    LOWER(SUBSTRING(last_name, 2))
  );
  
```

#### 5.4.2 数学函数

````mysql
/*
round(number) 四舍五入
round(number,length):四舍五入保留小数点后length位
ceil(number) 向上取整，返回 >= 该参数的最小整数
      如：SELECT CEIL(5.0001);  结果 6
floor(number) 向下取整，返回 <= 该参数的最大整数
      如：SELECT FLOOR(5.99999); 结果  5
truncate(number,length) 截断 小数点后保留length位数
      如：SELECT TRUNCATE(5.999999,2); 结果5.99
mod(num1,num2) 取模，符号看num1取正还是取负
      如：SELECT MOD(9,2);  结果： 1
*/
````

#### 5.4.3日期函数

```mysql
/*
now():返回当前系统日期+时间
     如: select now() 结果：2019-09-12 15:34:15
curdate():返回当前系统日期，
     如：select curdate()  结果：2019-09-12
curtime():返回当前系统时间
     如：select curtime() 结果：19:45:02
#获取指定的部分，年，月，日，小时，分钟，秒
  select year(now()) 获取年     2019
  select year('1996-12-17');   1996
  select MONTH('1996-12-17')   12
  select date('1996-12-17');    1996-12-17
  select monthname('1996-1-17'); 显示月份的英文写法  January
  
str_to_date('date','format') 将字符通过指定的格式转成日期
     如：SELECT STR_TO_DATE('12-17-96','%m-%d-%Y'); 结果：1996-12-17
date_formate('date','format') 将日期转成format样式显示 
     如：SELECT DATE_FORMAT(NOW(),'%m月%d日%Y年'); 结果 09月12日2019年
     
datediff(date,date):返回两个日期的天数差距
     如：SELECT 
  DATEDIFF(NOW(), '1996-12-17') ; 结果：8307
*/
```

#### 5.4.4 其他函数

```mysql
/*
select version() 查看当前数据库版本号
select database() 查看当前属于哪个数据库
select user() 查看当前用户
*/
```

#### 5.4.5 流程控制函数

```mysql
/*
 if(expr1,expr2,expr3) 类似于三元运算符
 如：select if(3>0,'大','小') 结果 大
*/
 SELECT 
  last_name,
  commission_pct,
  IF(
    commission_pct IS NULL,
    '没有奖金',
    '有奖金'
  ) 
FROM
  employees ;
 /*
  #case 语句
  case 要判断的字段或表达式
  when 常量1 then 要显示的值或语句
  when 常量2 then 要显示的值
  ...
  else 要显示的值n或语句
  end
*/
#查询员工的工资，部门号为30 显示的工资为1.1倍
SELECT 
    salary AS '原工资',
    department_id,
    CASE
      department_id 
      WHEN 30 
      THEN salary * 1.1 
      WHEN 40 
      THEN salary * 1.2 
      WHEN 50 
      THEN salary * 1.3 
      ELSE salary 
    END AS '新工资' 
FROM
  employees ;
/*case 函数的第二种方式
case
when 条件1 then 要显示的值或语句
when 条件2 then 要显示的值或语句
...
else 要显示的值或语句
end /
如：
/*
如果工资大于20000 显示A
大于15000 显示B
大于10000 显示C
其他，显示D
*/
SELECT 
  last_name,
  salary,
  CASE
    WHEN salary > 20000 
    THEN 'A' 
    WHEN salary > 15000 
    THEN 'B' 
    WHEN salary > 10000 
    THEN 'C' 
    ELSE 'D' 
  END AS '工资级别'
FROM
  employees ;
```

### 5.5 分组函数的使用

> 分组函数主要用作统计使用，又称为聚合函数或统计函数或函数组
>
> sum() , avg() , max() ,min(), count(),一些函数可以与 distinct 关键字使用
>
> 一般使用 count(*)用作统计行数
>
> 和分组函数一同查询的字段要求是 group by 后的字段
>
> 分组查询：
>
> select 分组函数，列(要求出现在group by 后面)
>
> from 表 【where 筛选条件】
>
> group by 分组的列表
>
> having  条件
>
> 【order by 子句】

```mysql
/*查询每个部门的平均工资*/
SELECT 
  AVG(salary),
  department_id 
FROM
  employees 
GROUP BY department_id ;

#查询每个位置上的部门个数
SELECT 
  COUNT(*),
  location_id 
FROM
  departments 
GROUP BY location_id ;

#查询每个工种的最高工资
SELECT 
  MAX(salary),
  job_id 
FROM
  employees 
GROUP BY job_id ;
```

#### 5.5.1 带有筛选条件的分组查询

>分组查询中的筛选条件分为两类
>
>​    1：分组前筛选
>
>​               *：数据源  位置：group by 子句前面  关键字：where
>
>​               *：原始表  位置：group by 子句前面    关键字：where
>
>​     2：分组后筛选
>
>​                *：分组后的结果集  位置：group by 子句后面  关键字：having
>
>分组函数做条件肯定是放在having子句中.
>
>能用分组前筛选的，就优先考虑使用分组前筛选.

```mysql
#查询邮箱中包含a字符的，每个部门的平均工资
SELECT 
  AVG(salary),
  department_id 
FROM
  employees 
WHERE email LIKE '%a%' 
GROUP BY department_id ;

#查询有奖金有每个领导手下员工的最高工资
SELECT 
  MAX(salary),
  manager_id 
FROM
  employees 
WHERE IFNULL(commission_pct, 0) > 0 
GROUP BY manager_id;

#查询哪个部门的员工个数大于2
SELECT 
  COUNT(1),
  department_id 
FROM
  employees 
GROUP BY department_id 
HAVING COUNT(*) > 2 ;

#查询每个工种有奖金的最高工资>12000的工种编号和最高工资
SELECT 
  MAX(salary),
  job_id 
FROM
  employees 
WHERE commission_pct IS NOT NULL 
GROUP BY job_id 
HAVING MAX(salary) > 12000 ;

#查询领导编号>102的每个领导手最低工资>5000的领导编号，和其最低工资
SELECT 
  MIN(salary),
  manager_id 
FROM
  employees 
WHERE manager_id > 102 
GROUP BY manager_id 
HAVING MIN(salary) > 5000 ;
```

#### 5.5.2 按表达式或函数分组

```mysql
# 按员工姓名的长度分组，查询每一组的员工个数，选出员工个数>5的有哪些
SELECT 
  COUNT(*),
  LENGTH(last_name) 
FROM
  employees 
GROUP BY LENGTH(last_name) 
HAVING COUNT(*) > 5 ;


```

#### 5.5.3 按多个字段进行分组

```mysql
#查询每个部门每个工种的员工的平均工资
SELECT 
  AVG(salary),
  department_id,
  job_id 
FROM
  employees 
GROUP BY department_id,
  job_id ;
  
#查询每个部门每个工种的员工的平均工资，并降序显示 
SELECT 
  AVG(salary),
  department_id,
  job_id 
FROM
  employees 
GROUP BY department_id,
  job_id 
ORDER BY AVG(salary) DESC ;
```

### 5.6 多表查询(连接查询)

> 按功能分类：
>
>   内连接：
>
> ​             等值连接
>
> ​             非等值连接
>
> ​              自连接
>
>   外连接：
>
> ​             左外连接
>
> ​             右外连接
>
> ​             全外连接
>
>   交叉连接：

#### 5.6.1 等值连接

> 多表等值连接的结果为多表的交集部分
>
> n表连接，至少需要 n-1 个连接条件
>
> 多表的顺序没有要求
>
> 一般需要为表取别名
>
> 可以搭配前面介绍的所有子句使用(排序，分组，筛选)

```mysql
#查询员工名和对应的部门名
SELECT 
  last_name,
  department_name 
FROM
  employees em,
  departments depart 
WHERE em.`department_id` = depart.`department_id` ;

#查询有奖金的员工名，部门名
SELECT 
  last_name,
  department_name,
  commission_pct 
FROM
  employees em,
  departments depart 
WHERE em.`department_id` = depart.`department_id` 
  AND em.`commission_pct` IS NOT NULL ;
  
#查询城市名中第二个字符为o的部门名和城市名
SELECT 
  department_name,
  city 
FROM
  departments de,
  locations lo 
WHERE de.`location_id` = lo.`location_id` 
  AND city LIKE '_o%' ;
  
#查询每个城市的部门个数
SELECT 
  COUNT(*),
  lo.`city` 
FROM
  departments de,
  locations lo 
WHERE de.`location_id` = lo.`location_id` 
GROUP BY lo.`city` ;

#查询有奖金的每个部门名和部门领导编号以及该部门的最低工资
SELECT 
  de.`department_name`,
  de.`manager_id`,
  MIN(em.`salary`) 
FROM
  departments de,
  employees em 
WHERE em.`department_id` = de.`department_id` 
  AND em.`commission_pct` IS NOT NULL 
GROUP BY de.`department_name`,
  de.`manager_id` ;

#查询每个工种的工种名和员工个数，并按员工个数降序
SELECT 
  job_title,
  COUNT(*) 
FROM
  employees em,
  jobs jo 
WHERE em.`job_id` = jo.`job_id` 
GROUP BY job_title 
ORDER BY COUNT(*) DESC ;

#查询员工名，部门名和所在的城市
SELECT 
  last_name,
  department_name,
  city 
FROM
  employees em,
  departments de,
  locations lo 
WHERE em.`department_id` = de.`department_id` 
  AND de.`location_id` = lo.`location_id` ;
```

#### 5.6.2 非等值连接

```mysql
#查询员工的工资和工资级别
SELECT 
  salary,
  grade_level 
FROM
  employees em,
  job_grades jg 
WHERE em.`salary` BETWEEN jg.`lowest_sal` 
  AND jg.`highest_sal` ;
```

#### 5.6.3 自连接

```mysql
#查询员工名和他上级的名字
SELECT 
  em.last_name '员工名',
  ma.last_name '领导名' 
FROM
  employees em,
  employees ma 
WHERE em.`manager_id` = ma.`employee_id` 
```

### 5.7 连接查询

> select 查询列表
>
> from 表1 别名
>
> join 表2 别名
>
> on 连接条件
>
> where 筛选条件
>
> 分类 ：   
>
> 内连接：inner
>
> 外连接
>
> ​     左外：left [outer]
>
> ​     右外：right [outer]
>
> ​      全外：full [outer]
>
> 交叉连接：cross 

#### 5.7.1 内连接

```mysql
/*查询名字中包含e的员工名和工种名*/
select 
  last_name,
  jo.`job_title` 
from
  employees em 
  join jobs jo 
    on em.`job_id` = jo.`job_id` 
where em.`last_name` like '%e%' ;

/*查询部门个数大于3的城市名和部门个数*/
select 
  lo.`city`,
  count(*) 
from
  locations lo 
  join departments de 
    on lo.`location_id` = de.`location_id` 
    group by lo.`city`
having count(*) > 3 ; 

/*查询哪个部门的部门员工个数大于3部门名和员工个数，并按个数降序*/
select 
  de.`department_name`,
  count(*) 
from
  employees em 
  join departments de 
    on em.`department_id` = de.`department_id` 
group by de.`department_name` 
having count(*) > 3
order by count(*) desc ; 

/*查询员工名，部门名，工种名，并按部门名降序*/
SELECT 
  em.`last_name`,
  de.`department_name` ,jo.`job_title`
FROM
  employees em 
   JOIN departments de 
    ON em.`department_id` = de.`department_id` 
   JOIN jobs jo 
    ON jo.`job_id` = em.`job_id` 
ORDER BY de.`department_name` DESC ;

#员工的名字带有字母k和他上级的名字
SELECT 
  em.`last_name`,
  ma.`last_name` 
FROM
  employees em 
  JOIN employees ma 
    ON em.`manager_id` = ma.`employee_id`
     WHERE em.`last_name` LIKE '%k%';
```

#### 5.7.2 外连接

> 外连接用于查询一个表中有另一个表中没有的数据
>
> 特点：
>
> * 外连接的查询结果为主表中的所有记录
> * 如果从表有和它匹配的，则显示匹配的值
> * 如果从表中没有和它匹配的，则显示null
> * 外连接查询结果=内连接结果+主表中有而从表中没有的数据 
> * 左外连接：left join 左边的是主表
> * 右外连接：right join 右边的是主表
> * 左外和右外交换两个表的顺序，可以实现同样的效果
> * 全外连接=内连接+表1中有但表2中没有的+表2中有但表1中没有的

```mysql
#查询男朋友，不在男神表的女神名
SELECT 
  b.name,
  bo.* 
FROM
  beauty b 
  LEFT JOIN boys bo 
    ON b.`boyfriend_id` = bo.`id` 
WHERE bo.`id` IS NOT NULL ;

#查询哪个部门没有员工
SELECT 
  de.*,
  em.`employee_id` 
FROM
  departments de 
  LEFT JOIN employees em 
    ON em.`department_id` = de.`department_id` 
WHERE em.`employee_id` IS NULL ;
```

#### 5.7.3 交叉连接

```mysql
SELECT b.*,bo.*
FROM beauty b
CROSS JOIN boys bo;
```

### 5.8 子查询

> 含义：出现在其他语句中的select语句，称主子查询或内查询
>
> 外部的查询语句，称为主查询或外查询
>
> 分类 ：
>
> * 按子查询出现的位置：select后面(仅仅支持标量子查询)，from后面（支持表子查询），**where或having后面（标量子查询，列子查询，行子查询）**，exists后面（表子查询）
> * 按结果集的行列数不同：标量子查询(结果集只有一行一列)，列子查询(结果集只有一列多行)，行子查询(结果集只有一行多列),表子查询（结果集一般为多行多列）
> * 子查询放在小括号内
> * 子查询一般放在条件的右侧
> * 标量子查询，一般搭配单行操作符使用(> , <,  >= , <= , = ,<> )
> * 列子查询，一般搭配多行操作符使用(in any/some,all)

````mysql

#谁的工资比Abel高？
SELECT 
  * 
FROM
  employees 
WHERE salary > 
  (SELECT 
    salary 
  FROM
    employees 
  WHERE last_name = 'Abel') ;
  
#返回job_id与141号员工相同，salary比142号员工多的员工姓名，job_id，工资
SELECT 
  em.`last_name`,
  em.`job_id`,
  em.`salary` 
FROM
  employees em 
WHERE em.`job_id` = 
  (SELECT 
    em.`job_id` 
  FROM
    employees em 
  WHERE em.`employee_id` = 141) 
  AND em.`salary` > 
  (SELECT 
    em.`salary` 
  FROM
    employees em 
  WHERE em.`employee_id` = 143) ;
  
#返回公司工资最少的员工的last_name,job_id和salary
SELECT 
  last_name,
  job_id,
  salary 
FROM
  employees 
WHERE salary = 
  (SELECT 
    MIN(salary) 
  FROM
    employees) ;

#查询最低工资大于50号部门最低工资的各个部门id和其最低工资
SELECT 
  MIN(salary),
  department_id 
FROM
  employees em 
GROUP BY department_id 
HAVING MIN(salary) > 
  (SELECT 
    MIN(salary) 
  FROM
    employees em 
  WHERE em.`department_id` = 50) ;
````

#### 5.8.1 列子查询

> 返回多行，使用多行比较操作符
>
> * IN/NOT IN 等于列表中任意一个
> * ANY|SOME 和子查询返回的某一个值比较，可以用 MAX()代替
> * ALL 和子查询返回的所有值比较，可以用 MIN()代替

```mysql
#返回location_id是1400或1700的部门中的所有员工和姓名 
SELECT 
  last_name 
FROM
  employees em 
WHERE em.`department_id` IN 
  (SELECT DISTINCT 
    de.`department_id` 
  FROM
    departments de 
  WHERE de.`location_id` IN (1400, 1700)) ;

#返回其他工种中比job_id为"IT_PROG"部门任一工资低的员工的工号，姓名,job_id,salary

SELECT 
  empl.`employee_id`,
  empl.`last_name`,
  empl.`job_id`,
  empl.`salary` 
FROM
  employees empl 
WHERE empl.`salary` < 
  (SELECT 
    MAX(salary) 
  FROM
    employees em 
  WHERE em.`job_id` = 'IT_PROG') 
  AND empl.`job_id` <> 'IT_PROG' ;

#返回其他部门中比job_id为'IT_PROG'部门所有工资都低的员工号，姓名，job_id，salary
SELECT 
  empl.`employee_id`,
  empl.`job_id`,
  empl.`last_name`,
  empl.`salary` 
FROM
  employees empl 
WHERE empl.`salary` < 
  (SELECT 
    MIN(salary) 
  FROM
    employees em 
  WHERE em.`job_id` = 'IT_PROG')
```

#### 5.8.2 行子查询

> 结果集(一行多列或多行多列)

```mysql
#查询员工编号最小并且工资最高的员工信息
SELECT 
  * 
FROM
  employees 
WHERE (employee_id, salary) = 
  (SELECT 
    MIN(employee_id),
    MAX(salary) 
  FROM
    employees) ;

#查询每个部门的员工个数
SELECT 
  d.department_id,
  (SELECT 
    COUNT(*) 
  FROM
    employees e 
  WHERE e.`department_id` = d.`department_id`) AS '个数' 
FROM
  departments d ;

#查询员工号=102的部门名
SELECT 
  d.`department_name` 
FROM
  departments d 
  JOIN employees em 
    ON d.`department_id` = em.`department_id` 
WHERE em.`employee_id` = 102 ;

#查询每个部门的平均工资的工资等级
SELECT 
  jg.`grade_level`,
  av_salary.* 
FROM
  (SELECT 
    AVG(salary) av,
    department_id 
  FROM
    employees 
  GROUP BY department_id) av_salary,
  job_grades jg 
WHERE av BETWEEN jg.`lowest_sal` 
  AND jg.`highest_sal` ;
```

### 5.9 分页查询

> limit 起始索引(从开始)  , 要显示的条目个数
>
> limit语句放在查询语句的最后
>
> 公式：要显示的页数 page,每页的条目数
>
> select 查询列表
>
> from 表
>
> limit (page-1)*size,size

```mysql
#查询前五条员工信息
SELECT 
  * 
FROM
  employees 
LIMIT 0, 5 

#查询第11条到25条
SELECT 
  * 
FROM
  employees 
LIMIT 10, 15 

#有奖金的员工信息，并且工资较高的前10名显示出来 
SELECT 
  * 
FROM
  employees 
WHERE IFNULL(commission_pct, 0) > 0 
ORDER BY salary DESC 
LIMIT 0, 10 ;


```

### 5.10 联合查询

> union:将多条查询语句的结果合并成一个结果
>
> 语法：
>
> 查询语句1
>
> union
>
> 查询语句2
>
> union
>
> ......
>
> 应用场景：要查询的结果来自于多个表，且多个表没有直接的关系，但要查询的信息一致。
>
> 特点：
>
> * 要求多条查询语句的查询列数是一致的
> * 要求多条查询语句的每一列的类型和顺序最好一致
> * union 关键字默认是去重，如果使用 union all 可以包含重复项

```mysql
#查询部门编号大于90或者邮箱中包括a的员工信息
SELECT 
  * 
FROM
  employees 
WHERE department_id > 90 
UNION
SELECT 
  * 
FROM
  employees 
WHERE email LIKE '%a%' ;
```

## 6. DML语句

> 数据操作语言：插入，修改，删除

### 6.1 插入语句

> 语法：
>
> - **insert into 表名( 列名,...) values (值1,...);**
> - **insert into 表名 set 列名=值，列名=值....**
>
> 两种语法的区别：
>
> * 语法一支持一次性插入多行数据  
> * 语法一支持子查询，不需要再写 values 关键字，查询出的结果集直接作为要插入的数据

```mysql
#
INSERT INTO beauty (id, NAME, phone) 
SELECT 
  26,
  '李四',
  '45564' ;
```

### 6.2 修改语句

>修改单表中记录 **update 表名 set 列=新值，列=新值 ... where 筛选条件;**
>
>修改多表的记录
>
>   1:  **update 表1 别名，表2 别名 set 列=值,...where 连接条件 and 筛选条件**
>
>   2:  **update 表1 别名 inner | left | right join 表2 别名 on 连接条件 set 列=值,... where 筛选条件** 

```mysql
UPDATE 
  beauty 
SET
  phone = '5555' 
WHERE NAME LIKE '%柳%' 

#修改多表的记录，修改张无忌的女朋友的手机号为114
UPDATE 
  beauty gri 
  JOIN boys boy 
    ON gri.`boyfriend_id` = boy.`id` SET gri.`phone` = '114' 
WHERE boy.`boyName` = '张无忌' ;
```

### 6.3 删除语句

> 语法：
>
> * delete from  表名 where 筛选条件;
> * truncate table 表名; 清空表中所有的数据

> **delete与truncate的区别**：
>
> * delete 可以加where条件，truncate不可以
> * truncate清空表数据，效率更高
> * 要删除的表中有自增长列，如果用delete删除，再插入数据，自增长列的值从断点开始，而truncate删除后，再插入数据，自增长值从1开始。
> * truncate删除没有返回值，delete删除有返回值
> * truncate删除不能回滚，delete删除可以回滚

```mysql
#多表的删除，删除张无忌的女朋友的信息
DELETE 
  gir.* 
FROM
  beauty gir 
  JOIN boys bo 
    ON gir.boyfriend_id = bo.id 
WHERE bo.`boyName` = '张无忌' ;

#删除黄晓明的信息以及他女朋友的信息
DELETE 
  bo.*,
  gir.* 
FROM
  boys bo 
  JOIN beauty gir 
    ON gir.boyfriend_id = bo.id 
WHERE bo.boyName = '黄晓明' ;
```

```mysql
#使用 truncate 语句
truncate table boys  #清空表中所有的数据
```

## 7.DDL语句

> 数据定义语言
>
> * 库的管理(创建，修改，删除)
> * 表的管理(创建，修改，删除)
> * 创建 create
> * 修改 alter
> * 删除 drop

### 7.1 库的管理

> 库的创建 ： create datebase 库名

```mysql
#创建库
create database if not exists test

#库的修改,更改库的字符集
ALTER DATABASE girls CHARACTER SET gbk;

#库的删除
drop database if exists test;
```

### 7.2 表的管理

> 表的创建： create table if exists表名(
>
>    列名  列的类型 【长度，约束】,
>
>    .....
>
> )
>
> 表的修改： 列名，列的类型或约束，添加列，删除列，修改表名
>
> 语法：**alter table 表名 add | drop | modify|change column 列名 列类型 约束**
>
> 通用写法：
>
>  **drop database if exists 库名**
>
> **create database 库名**
>
> **drop table if exists 表名**
>
> **create table 表名**

```mysql
#修改列名,修改列名时最后必须加上列名的数据类型
ALTER TABLE author 
  CHANGE COLUMN id au_id INT ;
  
#修改列的数据类型
ALTER TABLE t_book 
  MODIFY COLUMN publish_date TIMESTAMP ;

#添加新列
ALTER TABLE author 
  ADD COLUMN salary DOUBLE ;

#删除列
ALTER TABLE author 
  DROP COLUMN salary ;

#表重命名
ALTER TABLE author 
  RENAME TO t_author ;

#表的删除
DROP TABLE if exists t_author ;

#复制表的结构
CREATE TABLE cp_author LIKE t_author;

#复制表的所有数据+结构
CREATE TABLE cp_author2 
SELECT 
  * 
FROM
  t_author ;

#复制表的部分数据+结构，使用where筛选条件
CREATE TABLE cp_author3 
SELECT 
  * 
FROM
  t_author 
WHERE salary > 600 ;

#仅仅复制表中某些字段的结构
CREATE TABLE cp_boot2 
SELECT 
  id,
  bName,
  price 
FROM
  t_book 
WHERE 1 = 2 或者  where 0
```

### 7.3 常见的数据类型

> * 数值型：整型，浮点型
> * 字符型：较短的文件(char,varchar), 较长的文件(text),blok(较长的二进制数据 )
> * 日期型：

#### 7.3.1 整型

> * **tinyint , smallint , mediumint , int/integer , bigint (范围从左往右越来越大)**
> * 如果不设置无符号还是有符号，默认是有符号，如果想设置有符号，需要在数据类型后面添加关键字**unsigned**
> * 如果插入的数据超出了整型的范围，会报out of range 异常，并且插入它的临界值
> * 如果设置长度，默认会有长度
> *  长度代表了显示的最大宽度，如果不够会在左边用0填充，但必须使用关键字 **zerofill** ,而且默认使用该关键字后变成 无符号型

#### 7.3.2 小数

> 小数又分为 浮点型 (float(M,D) , double(M,D))，定点型 (DEC(M,D))
>
> 特点：
>
> * M和D M: 代表整数部位+小数部位的总长度
>
>   ​           D:  小数部位长度 
>
>   如果超过范围，则插入临界值
>
>   M和D都可以省略，但如果有DEC类型默认为 DEC(10,0) ，即不能有小数位存在
>
>   如果是float 和double，则会根据插入的数值的精度来决定精度
>
>   定点型的精度较高，如果要求插入数值的精度较高如货币运算等则优先考虑使用

> 选择使用类型的原则：所选择的类型越简单越好，能保存数值的类型越小越好。

#### 7.3.3 字符型 

>较短的文本：char   varchar
>
>较长的文本：text  blob(较大的二进制)
>
>binary和varbinary 用于保存较短的二进制
>
>enum 用于保存枚举
>
>set 用于保存集合

#### 7.3.4 日期型

> date:只保存日期
>
> time:只保存时间
>
> year:只保存年
>
> datetime:保存日期+时间
>
> timestamp:保存日期+时间
>
> 特点：
>
> * datetime  范围1000-9999     不受时区影响
> * timestamp  范围1970-2038    受时区影响,但更能反映真实时间

### 7.4 常见约束

> 约束：一种限制，用于限制表中的数据，为了保证表中的数据一致性
>
> 分类 ：
>
> * not null 非空约束，用于保证该字段的值不能为空
> * default  默认约束，用于保证该字段有默认值 
> * primary key  主键约束，用于保证该字段的值具有唯一性，并且非空
> * unique 唯一约束，用于保证该字段的值暗有唯一性，但可以为空
> * check 检查约束【mysql中不支持】
> * forgien key  外键约束，用于限制两个表的关系，用于保证该字段的值必须来自主表关联列的值 

>添加约束的时机：
>
>* 创建表时
>* 修改表时

> 约束的添加分类 ：
>
> * 列级约束 ：
>
>   ​          六大约束语法上都支持，但外键约束没有效果
>
> * 表级约束 ：
>
>   ​         除了 not null ,default 其他都支持

### 7.5 添加列级约束

````mysql
CREATE TABLE t_student(
   id INT PRIMARY KEY,
   stuName VARCHAR(20) NOT NULL,
   gender CHAR(1) CHECK(gender='男' OR gender='女'),
   seat INT UNIQUE,
   age INT DEFAULT 22,
   majorId INT FOREIGN KEY REFERENCES t_major(id)
);
````

### 7.6 添加表级约束

> 语法：在各个字段的最下面
>
> 【constraint 约束名】  约束类型(字段名)
>
> 主键：保证唯一性，不可为空，一个表至多有一个，允许组合 
>
> 唯一性：保证唯一性，可为空，一个表中可以有多个，允许组合
>
> 外键：
>
> * 要求在从表设置外键关系
> * 从表的外键列的类型和主表的关联列要求一致或兼容
> * 主表的关联列必须是一个key(主键或唯一键)
> * 插入数据时，先插入主表，再插入从表
> * 删除数据时，先删除从表，再删除主表

```mysql
CREATE TABLE t_student (
  id INT,
  stuName VARCHAR (20),
  gender CHAR(1),
  seat INT,
  age INT,
  majorId INT,
  #主键
  CONSTRAINT pk PRIMARY KEY (id),
  #唯一键
  CONSTRAINT uq UNIQUE (seat),
  #外键
  CONSTRAINT fk_student_major FOREIGN KEY (majorId) REFERENCES t_major (id)
) ;
```

### 7.7 修改表时添加约束

> 添加列级约束：
>
> ​         alter table 表名 modify column 字段名 字段类型 新约束
>
> 添加表级约束：
>
> ​         alter table 表名  add 【constraint 约束名】约束类型(字段名) 【外键的引用】

```mysql
#给列添加非空约束
ALTER TABLE t_student 
  MODIFY COLUMN stuName VARCHAR (20) NOT NULL ;
  
#添加主键约束
alter table t_student modify column id int primary key;
alter table t_student add constraint stu_pk primary key(id);

#添加唯一约束
alter table t_student modify column seat int unique;
alter table t_student add unique(seat);

#添加外键
alter table t_student add constraint fk_student_major foreign key(majorId) references major(id);
```

### 7.8 修改表时删除约束

```mysql
#删除非空约束
alter table t_student modify column stuName varchar(20) null;

#删除默认约束
alter table t_student modify column age int;

#删除主键
alter table t_student drop primary key; 或者 
alter table t_student modify column id int;

#删除唯一性
alter table t_student drop index 字段名

#删除外键
alter table t_student drop foreign key 字段名
```

### 7.9 标识列(自增长列)

> 可以不用手动的插入值，系统提供默认的序列值(从1开始)
>
> 标识列不一定和主键搭配，但要求字段必须是一个key
>
> 一个表中至多有一个自增长属性
>
> 自增长属性必须用于数值型，不可以是字符型

```mysql
#查看系统每次自增量(默认+1)和起始值
SHOW VARIABLES LIKE '%auto_increment%'

#设置系统每次自增量
SET auto_increment_increment=3;

#修改表时设置标识列
alter table t_student modify column id int primary key auto_increment;

#修改表时删除标识列
alter table t_student modify column id int primary key;
```

### 7.10 级联删除与级联置空

```mysql
#设置级联删除
alter table t_person add constraint fk_stu_person foreign key(id) references t_stu(id) on delete cascade;

#设置级联置空
alter table t_person add constraint fk_stu_person foreign key(id) references t_stu(id) on delete set null;
```

## 8.TCL语句

> Transaction Control Language : 事务控制力语言
>
> 一个或一组sql语句组成一个执行单元，这个执行单元要么全部执行，要么全部不执行
>
> 应用场景：转帐等...
>
> **事务的ACID属性**
>
> * **原子性(Atomicity)：是指事务是一个不可分割的工作单位，事务中的操作要么都发生，要么都不发生。**
> * **一致性(Consistency): 事务必须使数据库从一个一致性状态变换到另外一个一致性状态。**
> * **隔离性(Isolation): 是指一个事务的执行不能被其他事务干扰，即一个事务内部的操作及使用的数据对并发的其他事务是隔离的，并发执行的各个事务之间不能互相干扰。**
> * **持久性(Durability): 指一个事务一旦被提交，它对数据库中数据的改变就是永久性的，接下来的其他操作和数据库故障不应该对其有任何影响。**

### 8.1 事务的创建 

> 隐式事务：事务没有明显的开启和结束的标记(insert,update,delete 语句),这些语句的事务，默认开启，即自动提交。
>
> 显式事务：事务具有明显的开启和结束的标记，前提是先设置自动提交功能关闭.
>
> set automommit = 0
>
> 使用事务的步骤:
>
> * 开启事务
>
>   set autocommit=0;
>
>   start transaction;【可选】
>
> * 编写事务中的sql语句(select,insert,update,delete)
>
> * 结束事务
>
>     commit; 提交事务
>
>     rollback;回滚事务

```mysql
SET autocommit=0; #关闭事务自动提交
START TRANSACTION; #开启事务
UPDATE t_person SET salary=999 WHERE id=2;
INSERT t_person VALUES (4,'wmy',666);
COMMIT; #提交事务
```

### 8.2 事务并发产生的问题

> 对于同时运行的多个事务, 当这些事务访问数据库中相同的数据时, 如果没有采取必要的隔离机制, 就会导致各种并发问题:
>
> * **脏读: (**针对未提交的数据**)一个事务在更新一条记录，未提交前，第二个事务读到了第一个事务更新后的记录，那么第二个事务就读到了脏数据，会产生对第一个未提交。**数据的依赖。一旦第一个事务回滚，那么第二个事务读到的数据，将是错误的脏数据。
> * **不可重复读:（**读取数据本身的对比**）一个事务在读取某些数据后的一段时间后，再次读取这个数据，发现其读取出来的数据内容已经发生了改变，就是不可重复读。**
> * **幻读: （**读取结果集条数的对比**）一个事务按相同的查询条件查询之前检索过的数据，确发现检索出来的结果集条数变多或者减少（由其他事务插入、删除的），类似产生幻觉。**
> * **更新丢失：最后的更新覆盖了其他事务之前的更新，而事务之间并不知道，发生更新丢失。更新丢失，可以完全避免，应用对访问的数据加锁即可。**
> * 数据库事务的隔离性: 数据库系统必须具有隔离并发运行各个事务的能力, 使它们不会相互影响, 避免各种并发问题
> * 一个事务与其他事务隔离的程度称为隔离级别. 数据库规定了多种事务隔离级别, **不同隔离级别对应不同的干扰程度, 隔离级别越高, 数据一致性就越好, 但并发性越弱.**

### 8.3 MySQL支持的四种事务隔离级别

> Innodb就是默认的存储引擎，Innodb最大的特点是：支持事务、支持行级锁。
>
> 既然支持事务，那么就会有处理并发事务带来的问题：更新丢失、脏读、不可重复读、幻读；相应的为了解决这四个问题，
>
> 就产生了事务隔离级别：**未提交读（Read uncommitted），已提交读(Read committed)，可重复读(Repeatable read)，可序列化(Serializable)**。
>
> **MySQL 默认的级别是:Repeatable read 可重复读。**

|          隔离级别          |               读数据一致性               | 脏读 | 不可重复读 | 幻读 |
| :------------------------: | :--------------------------------------: | :--: | :--------: | :--: |
| 未提交读(Read uncommitted) | 最低级别，只能保证不读取物理上损坏的数据 |  是  |     是     |  是  |
|  已提交读(Read committed)  |                  语句级                  |  否  |     是     |  是  |
| 可重复读(Repeatable read)  |                  事务级                  |  否  |     否     |  是  |
|   可序列化(Serializable)   |             最高级别，事务级             |  否  |     否     |  否  |

```mysql
# 查看当前系统的隔离级别
select @@tx_isolation;

#设置当前MySQL的隔离级别
set session transaction isolation level read committed

#设置数据库系统的全局隔离级别
set global transaction isolation level read committed
```

### 8.4 不可重复读与幻读的区别

>不可重复读
>
>重点是**修改**：同样的条件下，你读取过的数据，再次读取发现值不一样了。
>
>例子：（只是为了说明区别，没有事务隔离级别约束）
>
>事务1：Jack读取自己的工资为1000，事务并没有结束：
>
>select salary from employee empId ="Jack"; 
>
> 
>
>事务2：财务人员修改了Jack的工资为2000，并提交事务（事务1不知道）：
>
>update employee set salary = 2000;
>
>commit;
>
>在事务1中，再次读取Jack的工资时，工资变为2000
>
>select salary from employee empId ="Jack";
>
> 
>
>**在一个事务中前后两次读取的结果值并不一致，导致了不可重读读**
>
> 
>
>幻读
>
>重点在于**新增或者删除（数据条数的变化）**：同样条件下，第一次和第二次读出来的记录条数不一样。
>
>例子：
>
>目前工资为1000的员工有10人。
>
>事务1：读取所有工资为1000的员工，事务进行中
>
>select count(*) from employee where salary = 1000;
>
>共读取到10条记录。
>
>事务2：同时插入一条新的员工记录，工资也为1000
>
>insert into employee(empId, salary) values('Tom', 1000);
>
>commit;
>
>事务1：再次读取所有工资为1000的员工
>
>select count(*) from employee where salary = 1000;
>
>共读取到了11条记录，这样就像产生了幻读。

### 8.5 设置保存点(savepoint)

```mysql
SET autocommit=0;
START TRANSACTION;
DELETE FROM employees WHERE employee_id=128;
SAVEPOINT a;#设置保存点
DELETE FROM employees WHERE employee_id=129;
ROLLBACK TO a;#回滚到保存点
```

### 8.6 视图

#### 8.6.1 创建视图

> 虚拟表，和普通表一样使用
>
> 通过表动态生成的数据

```mysql
#查询姓名中包含a字符的员工名，部门名，和工种信息
CREATE VIEW v1 AS  #创建视图
SELECT 
  em.last_name,
  de.department_name,
  jo.job_title 
FROM
  employees em 
  JOIN departments de 
    ON em.department_id = de.department_id 
  JOIN jobs jo 
    ON em.job_id = jo.job_id ;

SELECT #在视图中查询信息
  * 
FROM
  v1 
WHERE last_name LIKE '%a' ;

#查询各部门的平均工资级别
CREATE VIEW v2 AS 
SELECT 
  AVG(salary),
  department_id 
FROM
  employees 
GROUP BY department_id ;

SELECT 
  v2.`avg(salary)`,
  jd.`grade_level` 
FROM
  v2 
  JOIN job_grades jd 
    ON v2.`avg(salary)` BETWEEN jd.`lowest_sal` 
    AND jd.`highest_sal` ;
```

#### 8.6.2 视图的修改

> 方式一：
>
> create or replace view 视图名
>
> as
>
> 查询语句
>
> 方式二：
>
> alter view 视图名 
>
> as
>
> 查询语句

#### 8.6.3 删除视图

> drop view 视图名，视图名,....

```mysql
#查看视图结构
desc 视图名
show create view \G 视图名
```

#### 8.6.4 视图的更新

> 视图的可更新性和视图中查询的定义有关系，以下类型的视图是不能更新的。
> • 包含以下关键字的sql语句：分组函数、distinct、group by、having、union或者union all
> • 常量视图
> • Select中包含子查询
> • join
> • from一个不能更新的视图
> • where子句的子查询引用了from子句中的表

### 8.7 delete与truncate在事务使用时的区别

> delete支持事务回滚，而truncate不支持事务回滚

## 9. 变量

> 分类：
>
> * 系统变量
>
>   ​    全局变量
>
>   ​    会话变量
>
> * 自定义变量
>
>      用户变量
>
>      局部变量

### 9.1 系统变量

> 使用语法：
>
> 查看所有的系统变量：SHOW global | 【session】 VARIABLES
>
> 查看满足条件的部分系统变量   SHOW global | session VARIABLES like '%...%'
>
> 查看指定的某个系统变量 select @@global | 【session.系统变量名;
>
> 为某个系统变量赋值 set global | 【session】系统变量名  = 值 或者
>
> ​                                     set @@global |【session】.系统变量名=值 

```mysql
#查看所有的全局变量
show global variables;

#查看部分的全局变量
show global variables like '%char%'

#查看指定的全局变量的值
select @@global.autocommit;

#为某个指定的全局变量赋值
set @@global.autocommit=0;
```

### 9.2 自定义变量

> 声明 赋值 使用
>
> * 声明并初始化：作用域：针对于当前会话(连接)有效，同于会话变量的作用域
>
>   ​                set  @用户变量名=值  或
>
>   ​                set  @用户变量名:=值   或
>
>   ​                select @用户变量名:=值
>
> * 赋初值:
>
>   ​                 set  @用户变量名=值  或
>
>   ​                set  @用户变量名:=值   或
>
>   ​                select @用户变量名:=值 或
>
>   ​                select 字段  into  变量名  form  表

```mysql
#查看用户变量值
select @用户变量名
```

> 局部变量：
>
> 作用域：仅仅在定义它的begin,end 中有效,且为第一句
>
> 声明： declare 变量名  类型;
>
> ​             declare 变量名 类型 default 值
>
> 赋值：
>
> ​              set  局部变量名=值  或
>
> ​              set  局部变量名:=值   或
>
> ​              select @局部变量名:=值
>
> ​              select 字段  into  局部变量名  form  表

```mysql
#查看局部变量名值
select 局部变量名
```

### 9.3 用户变量与局部变量的区别

|          | 作用域       | 定义和使用的位置               |           语法            |
| :------: | ------------ | ------------------------------ | :-----------------------: |
| 用户变量 | 当前会话中   | 会话中的任何地方               | 必须加@符号，不用限定类型 |
| 局部变量 | begin end 中 | 只能在begin,end 中，且为第一句 | 不加用@符号，需要限定类型 |

```mysql
#声明两个变量并赋初值，就和，并打印
SET @a=1;
SET @b=2;
SET @sum=@a+@b;

SELECT @sum;
```

## 10. 存储过程

> 一组预先编译好的SQL语句的集合，理解成批处理语句
>
> 类似java中的方法
>
> 提高 代码重用性，简化操作，减少了编译次数并且减少了和数据库服务器的连接次数
>
> 创建语法：  create procedure 存储过程名(参数列表)
>
> ​                       begin
>
> ​                              存储过程体（一组SQL语句)
>
> ​                       end
>
> 参数列表包含三部分：参数模式，参数名，参数类型
>
> ​                                         in            stuName          varchar(20)
>
> 参数模式：in(方法参数的传入值)    out(方法的返回值)     inout(即可以传入值，也可以返回值)
>
> 如果存储过程体中仅仅只有一句话，begin  end  可以省略
>
> 存储过程体中的每条SQL语句的结尾必须要加分号
>
> 存储过程的结尾可以使用 delimiter 重新设置
>
>    语法： delimiter  结束标记                      如  delimiter $
>
> 
>
> 调用存储过程 ：
>
> call 存储过程名(实参列表)

```mysql
#插入到admin表中1条记录
DELIMITER $
#声明存储过程
CREATE PROCEDURE myp2()
BEGIN
   INSERT INTO admin
   VALUES(8,'wangu','456');
END $
#调用存储过程 
CALL myp2();
```

 ```mysql
#创建存储过程实现，根据女神名，查询对应的男神信息
DELIMITER $

CREATE PROCEDURE vv2(IN gName VARCHAR(20))
BEGIN
   SELECT
   bo.*
   FROM
   boys bo
   RIGHT JOIN
   beauty b
   ON bo.id=b.boyfriend_id
   WHERE b.name=gName;
END $

CALL vv2('Angelababy');

#查询用户名密码是否存在于表中
DELIMITER $
CREATE PROCEDURE vv5 (
  IN username VARCHAR (20),
  IN PASSWORD VARCHAR (20)
) 
BEGIN
  DECLARE result INT DEFAULT 0 ; #定义局部变量
  SELECT 
    COUNT(*) INTO result #将查询结果赋值给局部变量
  FROM
    admin 
  WHERE admin.username = username 
    AND admin.password = PASSWORD ;
  SELECT 
    IF(
      result > 0,
      '用户存在',
      '用户不存在'
    ) ;
END $

CALL vv5 ('lyt', '6666');#调用存储过程 
 ```

````mysql
#创建带out模式的存储过程 
#根据女神名，返回对应的男神名
DELIMITER $
##定义存储过程
CREATE PROCEDURE vv6(IN gName VARCHAR(20),OUT bName VARCHAR(20))
BEGIN
   SELECT
   b.boyName INTO bName  #将结果赋值给bName，并返回结果
   FROM
   boys b
   JOIN beauty g
   ON b.id=g.boyfriend_id
   WHERE g.name=gName;
END $

SET @bName; #定义用户变量
CALL vv6('Angelababy',@bName);#调用存储过程
SELECT @bName;#输出存储过程返回值
````

```mysql
#当有多个返回结果时
#根据女神名返回对应男神的名字和魅力值
DELIMITER $

CREATE PROCEDURE vv7(IN gName VARCHAR(20),OUT bName VARCHAR(20),OUT cp INT)
BEGIN
   SELECT
   bo.boyName,#注意对多个返回结果的赋值，写法与一个返回结果不同
   bo.userCP INTO bName,cp
   FROM
   boys bo
   JOIN beauty gi
   ON bo.id=gi.boyfriend_id
   WHERE gi.name=gName;
END $

SET @bName;
SET @cp;
CALL vv7('Angelababy',@bName,@cp);
SELECT @bName,@cp;
```

```mysql
#创建带 inout 模式参数的存储过程
#传拉a和b两个值，最终a和b都翻倍并返回
DELIMITER $

CREATE PROCEDURE vv8(INOUT a INT,INOUT b INT)
BEGIN
   SET a=a*2;
   SET b=b*2;
END $

SET @a=2;
SET @b=4;
CALL vv8(@a,@b);

SELECT @a,@b;
```

```mysql
#创建存储过程或函数实现传入用户名和密码，插入到admin表中
DELIMITER $

CREATE PROCEDURE vv1(IN username VARCHAR(20),IN passwd VARCHAR(20))
BEGIN
    INSERT INTO 
    admin(admin.username,PASSWORD)
    VALUES(username,passwd);
END $

#创建存储过程实现传入一个日期，格式化成xx年xx月xx日并返回
DELIMITER $

CREATE PROCEDURE vv5(IN myDate DATETIME,OUT str VARCHAR(20))
BEGIN
   SELECT DATE_FORMAT(myDate,'%y年%m月%d日') INTO str;
END $

CALL vv5(NOW(),@str);
SELECT @str
```

### 10.1 存储过程的删除

> 存储过程只能单个删除

```mysql
drop procedure 存储过程名
```

### 10.2 查看存储过程的结构

```mysql
show create procedure 存储过程名
```

## 11.函数

> 存储过程与函数的返回值
>
> 存储过程可以没有返回，也可以有多个返回,适合做批量插入，批量更新
>
> 函数必须只能有一个返回，适合做处理数据后返回一个结果
>
> 
>
> 创建语法：
>
> 参数列表包含两部分：参数名   参数类型
>
>   create functon 函数名(参数列表)  returns 返回类型
>
> begin
>
> ​     函数体(必须要有 return 语句，没有会报错)
>
> end
>
> 如果 return 语句没有放在函数体的最后也不会报错，但不建议
>
> 当函数体中仅有一句话，则可以省略 begin end;
>
> 使用 delimiter 语句设置结束标记
>
> 
>
> 调用语法：
>
> select  函数名(参数列表)

```mysql
#返回公司的员工个数,无参有返回
DELIMITER $

CREATE FUNCTION f1() RETURNS INT
BEGIN
  DECLARE c INT DEFAULT 0;
  SELECT COUNT(*) INTO c
  FROM employees;
  RETURN c;
END $

SELECT f1();

#有参有返回
#根据员工名，返回它的工资
DELIMITER $

CREATE FUNCTION f3(NAME VARCHAR(20)) RETURNS DOUBLE
BEGIN
   DECLARE money DOUBLE DEFAULT 0;
   SELECT 
   salary INTO money
   FROM
   employees
   WHERE
   last_name=NAME;
   RETURN money;
END $

SELECT f3('Kochhar');

#根据部门名，返回该部门的平均工资
DELIMITER $

CREATE FUNCTION f5(departName VARCHAR(20)) RETURNS DOUBLE
BEGIN
   DECLARE money DOUBLE;
   SELECT
   AVG(salary) INTO money
   FROM
   employees em
   JOIN departments de
   ON em.department_id=de.department_id
   WHERE de.department_name=departName;
   RETURN money;
END $

SELECT f5('IT');
```

### 11.1 函数的查看与删除

```mysql
#查看函数结构
show create function 函数名

#删除函数
drop function 函数名
```

```mysql
#创建函数，实现传入两个float，返回二者之和
DELIMITER $

CREATE FUNCTION f6(n FLOAT,m FLOAT) RETURNS FLOAT
BEGIN
   DECLARE summary FLOAT DEFAULT 0.0;
   SELECT n+m INTO summary;
   RETURN summary;
END $

SELECT f6(1.1,0.2);
```

## 12. 流程控制

> 顺序结构：
>
> 分支结构：
>
> 循环结构：

### 12.1 case结构

> if 函数
>
> select if(表达式1，表达式2，表达式3) 类型java中if的三元运算符号
>
> case 函数
>
>   情况一：类型于java中switch语句，一般用于实现等值判断
>
> ​      语法：case 变量
>
> ​                  when 要判断的值  then 返回的值1
>
> ​                  when 要判断的值  then 返回的值2
>
> ​                  ....
>
> ​                   else    返回的值n
>
> ​                   end
>
>    情况二：类型于java中的多重if语句，一般用于实现区间判断
>
> ​          语法：case 变量
>
> ​                    when 要判断的条件1  then 返回的值1
>
> ​                    when 要判断的条件2  then 返回的值2
>
> ​                    ....
>
> ​                     else   返回的值n
>
> ​                     end
>
> 特点：
>
> 既可以作为表达式，嵌套在其他语句中使用，可以放在任何地方
>
> 也可以作为独立的语句去使用，只能放在 begin end 中

```mysql
#根据传入的成绩，去显示等级
DELIMITER $

CREATE PROCEDURE tt(IN score INT)
BEGIN
     CASE
     WHEN score BETWEEN 90 AND 100 THEN SELECT 'A';
     WHEN score BETWEEN 80 AND 90 THEN SELECT 'B';
     WHEN score BETWEEN 70 AND 80 THEN SELECT 'C';
     ELSE SELECT 'D';
     END CASE;
END $

CALL tt(55);
```

### 12.2 if结构

> 只能应用在begin,end 中，实现多重分支 
>
> if    条件1  then   语句1;
>
> elseif    条件2   then   语句2
>
> 【else】 语句n;
>
> end if;

```mysql
#根据传入的成绩，并返回显示等级
DELIMITER $

CREATE FUNCTION test_if(score INT) RETURNS CHAR
BEGIN
   IF score BETWEEN 90 AND 100 THEN RETURN 'A';
   ELSEIF score BETWEEN 80 AND 90 THEN RETURN 'B';
   ELSEIF score BETWEEN 70 AND 80 THEN RETURN 'C';
   ELSE RETURN 'D';
   END IF;
END $

SELECT test_if(55);
```

### 12.3 循环结构

> while , loop  ,repeat 只能放在 begin end 中
>
> 循环控制：iterate 类似于continure 结束本次循环，继续下一次
>
> leave 类似于 break  结束当前循环
>
> 
>
> while语法：
>
> while 循环条件
>
>   do
>
>    循环体;
>
> end while;
>
> 
>
> loop 语法：
>
> 【标签】loop
>
> ​              循环体;
>
> end loop【标签】 可以用来模拟简单的死循环
>
> 
>
> repeat 说法：
>
> 【标签】 repeat
>
> ​          循环体；
>
> until 结束循环的条件
>
> end repeat【标签】

```mysql
#批量插入，根据次数插入到admin表中多条记录
DELIMITER $

CREATE PROCEDURE t_while(IN num INT)
BEGIN
     
     DECLARE i INT DEFAULT 0;
     WHILE
     i < num DO
     INSERT INTO admin(username,PASSWORD)
     VALUES('vv'+i,666);
     SET i=i+1;
     END WHILE;
    
END $

CALL t_while(50);

SELECT COUNT(*) FROM admin;
```

```mysql
DELIMITER $

CREATE PROCEDURE ttv(IN num INT)
BEGIN
     
     DECLARE i INT DEFAULT 0;
     a:WHILE
     i < num DO
     INSERT INTO admin(username,PASSWORD)
     VALUES('cc'+i,999);
     SET i=i+1;
     IF i>20 THEN LEAVE a;
     END IF;
     END WHILE;
    
END $
```







