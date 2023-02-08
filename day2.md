# 模型02

~~~markdown
1. MTV 模型层
	ORM：对象关系映射
		将一个类映射称为数据库中的一张表
		将类中的属性映射称为表中的字段
		通过操作类以及操作类的属性来完成对于数据的操作
2. ORM开发步骤
	1. 安装驱动：pip install mysqlclient
	2. 配置settings：
			修改DATABASES属性
	3. 创建app，挂载app
	4. 在app的models文件中进行模型的定义
	5. 使用命令将模型迁移到数据库
		python manage.py makemigrations
		python manage.py migrate
3. 模型字段
	字段类型：略
~~~

# MySQL

~~~markdown
mysql：是一款数据库产品。数据库用来对数据进行存储和管理(增删改查)
数据库简称：DB
常见的DB：mysql/oracle/sqlserver/mongodb/redis

数据库存储数据：
	支持大量数据、效率高
	支持并发
	错误检查能力强
	数据相对安全
~~~

## 数据库的相关概念

~~~markdown
表(table)：存储数据
行(row)：代表表中的一条数据，对应的是一个model类对象。
列(column)：代表数据中的一项内容，字段，键key
	主键PK(primary key)：唯一标识表中的一条数据，特点唯一、非空
	外键FK(foreign key)：标识当前表和其他表的关系
~~~

## SQL命令

~~~markdown
SQL(structure query language)命令：结构化的查询语言，对关系型数据库中的数据增删改查操作。
~~~

#### 1. 简单查询

~~~markdown
1. 查询工号、姓、名、年薪
		select job_number,firstname,lastname,salary from emplyees;
2. 查询所有列
        select * from emplyees;
        select job_number,firstname,lastname,salary,married from emplyees;# 效率高
3. 对列进行计算
		select job_number,firstname,lastname,salary,salary*12 from emplyees;
4. 起别名
		select job_number,firstname,lastname,salary,salary*12 as 年薪 from emplyees;
5. 字符串拼接
		select job_number,CONCAT(firstname,"·",lastname) as 姓名,salary,salary*12 as 年薪 from emplyees;
~~~

#### 2. 排序

~~~markdown
语法：select 字段名1,字段名2..... from 表名 order by 字段名 asc(默认升序)|desc(降序),字段名2 asc|desc
# 根据薪资进行降序排序，如果薪资相同，则按照结婚次数排序
select * from emplyees ORDER BY salary desc , married desc;
~~~

#### 3. 条件查询

~~~markdown
语法： select ... from .... where 过滤条件
1. 等值比较
		select * from emplyees where salary=20;
2. 字符串等值比较
		select * from emplyees where lastname="wt";
3. 查询工资在一万到两万之间
		select * from emplyees WHERE salary < 20 and salary > 10;
4. 查询结婚次数是0次和3次的
		select * from emplyees WHERE married =0 or married=3;
5. 查询没有部门的员工
		select * from emplyees WHERE partment is NULL;
		select * from emplyees WHERE partment is not NULL;
6. 模糊查询
		%：通配符：代表0~n个任意字符
		_：代表任意一个字符
		查询lastname以w开头的员工
			select * from emplyees where lastname LIKE "w%";
		查询lastname不以w开头的员工
			select * from emplyees where lastname not LIKE "w%";
		查询倒数第三个字符为c的员工
			select * from emplyees where lastname LIKE "%c__";
		查询lastname长度为8，倒数第三个字符为c的员工
			select * from emplyees where lastname LIKE "_____c__";
~~~

#### 4. 函数

##### 1. 单行函数

~~~markdown
1. now()
	获取当前时间，有几行数据就返回几条
	select NOW() from emplyees;
2. date_format(日期类型数据,日期格式字符串)：将日期转换为指定的字符串
	%Y:4位的年
	%y:2位的年
	%m:2位的月
	%d:2位的天
	%H:小时---24小时
	%h:小时---12小时
	%i:2位的分钟
	%s:2为的秒
	select date_format(now(),"%Y年%m月%d日 %H:%i:%s") as time from emplyees;
~~~

##### 2. 组函数

~~~markdown
概念：函数作用于表中的一组数据，有一组数据就执行一次，如果没有对表进行分组操作那么这整张表就属于同一组
max()
min()
sum()
avg()
~~~

#### 5. group by 分组

~~~markdown
语法：select ... from ... where ... group by ...
统计各个公寓员工的人数：1. 公寓相同则归为一组
	select partment,count(salary),lastname from emplyees GROUP BY partment;
~~~

#### 6. having语句

~~~markdown
having:对分组后的结果进行过滤，符合条件的留下来
语法：select ... from ... where ... group by ... having ... order by...
每个部门的平均薪资，只显示平均薪资高于1W的
	select partment,AVG(salary) 平均薪资 from emplyees GROUP BY partment HAVING 平均薪资 > 10;

where：对分组前的数据进行过滤：效率高
having：先分组，后过滤
总结：如果过滤条件可以用where也可以用having，先使用where
过滤条件中使用分组后的数据，必须使用having
~~~

#### 7. 表连接

~~~markdown
表连接：查询的数据如果来源于多张表，需要把两张大表合并成一张大表进行查询，称为表连接。
类型：
	1. 内连接 [inner join]
	2. 外连接 
		左外连接[left join]
		右外连接[right join]
		全外连接[full join]
	3. 自连接 特殊的连接方式

表连接之后的大表：
	列数：各表列的和
	行：取决于表连接的类型
~~~

##### 1. 内连接

~~~markdown
语法：select ... from 表1 [inner] join 表2 on 连接条件 where ... group by ... having ... order by
- 查一个人的工号，姓，名，工资，所在部门的编号，所在部门的名称
select job_number,firstname,lastname,salary,department_name from emplyees INNER JOIN department on emplyees.partment = department.department_id;

内连接特点：
	1. 查询结果：左表和右表基于连接条件，匹配成功的数据作为查询后的总行数
	2. 必须有连接条件
	3. 左表和右表没有顺序要求

注意：如果存在字段名重复，必须用表名.字段名或者表别名.字段名进行区分
~~~

##### 2. 外连接

~~~markdown
语法：select ... from 表1 left|right|full join 表2 on 连接条件 where ... group by ... having ... order by

- 查询所有员工的工号、姓、名、工资、部门编号、部门名称

左外连接查询特点：
	1. 查询结果：以左表为主，左表和右表匹配上的数据+左表和右表没有匹配上的数据
	2. 必须有连接条件
	3. 左表和右表有顺序之分
	4. 没匹配上的数据，在显示的时候，显示对方的表字段为null

右外连接查询特点：
	1. 查询结果：以右表为主，左表和右表匹配上的数据+右表没有匹配上的数据
	2. 必须有连接条件
	3. 左表和右表有顺序

全外连接：
	mysql不支持，oracle支持全外连接
	1. 查询结果：左表和右表匹配上的数据+左表没有匹配上的数据+右表没有匹配上的数据
	2. 必须有连接条件
	3. 左表和右表没有顺序
~~~

##### 3. 自连接

~~~markdown
自连接：是一种特殊的表链接，通常选用内连接的语法
特殊：参与表链接的2张表或多张表都是同一张表

id  category_name level parent_id
1		后端语言	1		null
2		前端语言	1		null
3		java	   2		1
4		c++		   2        1
5       html       2        2
- 查询出所有二级类别对应的一级类别
先做表连接然后进行过滤
select * from category c1 left join category c2 on c1.parent_id = c2.id where c1.`level`=2;
先过滤再表连接
select * from (select * from category where level=2) c1 left join category c2 on c1.parent_id = c2.id;
~~~

##### 4. 多表链接

~~~markdown
select ... from 表1 join 表2 on 连接条件 join 表3 on 连接条件 ... 
多个left join即可
~~~

#### 8. 建表语句

~~~markdown
DDL:数据定义语言
create table 表名(
	字段名1 数据类型 [默认值][约束][约束],
	字段名2 数据类型,
	字段名3 数据类型---最后一个字段不加逗号
)
数据类型：
	int类型：整数，占用内存4字节
	double类型：代表小数，占用8字节，使用方式：double(5,2)第一个参数表示整体多少位，第二个参数表示小数点后多少位
	char(n)：固定长度字符串类型(n最大是2000)
	varchar(n)：可变长度字符串类型(n最大是4000)，最多分配n字节
	char(5)	varchar(5)
	'abc'     'abc'
	占空间		省空间
	效率高		效率低
日期类型：
	datetime：代表时间，存储格式年月日时分秒，占用8字节

约束：
	1. 主键约束(PK)primary key
		唯一、非空
		语法：primary key
		注意：开发的时候，每张表都会有主键
	2. 唯一约束
		该字段的值不可重复
		唯一，可以为空
		unique
	3. 非空约束
		可以重复，不能为空
		not null
	4. 外键
		该字段的值不能随便输入，必须是另一张表的主键或者唯一键存在的值
		可以重复，可以为null
		references

表：学生
id		name		class_id
1		qd				1
2		wt				2
3		dsh				1
4		qhh				5---错误，必须是班级表中主键的值

表：class
id		name
1		2021
2		2022
~~~

#### 事务

~~~markdown
概念：一个业务1~n条sql组成。为了保证业务的正确性，多条sql要么一起成功，要么一起失败

1. 回滚段
	数据库会为每一个连接上的客户端，开辟一小块空间(回滚段)，用于暂时存储sql语句的执行结果，当事务commit时，会把自己回滚段中的数据真正写入表，当事务rollback的时候，会清空自己回滚段中的数据。
	回滚段的特点：只要是写入操作，都会进入到回滚段中，写入操作：增删改
2. 锁：
	在一个事务中insert/update/delete 数据时，会获取改这条数据的标记，直到该事务结束(commit/rollback)，才会释放锁标记。其他客户端才能获取锁标记进行操作。
	注意：查询操作没有事务。
3. 事务的四大特性：
	ACID
	A：Atomic(原子性):事务中的多个sql为一个整体，都成功则提交，有一个失败则寄
	C：Consistency(一致性):事务结束后 和数据库中的数据状态必须一致
	I：Isolation(隔离性):多用户并发，用户和用户的数据是相互不影响的
	D：Durability(持久性):事务对数据的影响是持久的、深远的，不是临时的
~~~

# 一、 关联关系

## 1. 概述

~~~markdown
关联关系指的是数据表之间的数据是相互依赖和影响的，表之间有从属关系。比如：有一个用户表，用户又有一个订单表，用户表和订单表就有从属关系。
~~~

## 2. 关联关系的种类

~~~markdown
1. 一对一
	一个人一本护照
2. 一对多
	一个部门有多个员工
3. 多对多
	一个学生有多门课，一门课有多个学生
~~~

## 3. Model类中的关联关系

~~~markdown

~~~

### 五、 懒加载

~~~markdown
当Django的ORM进行数据查询的时候，QuerySet不会立即执行sql去查询数据，而是在数据被使用到的时候，才会查询。
延迟执行
ps = Person.objects.all() # 查询所有人的信息，但是实际上这个时候并没有执行select
print(ps)

# mysql添加日志
1. mysql安装目录下有个my.ini---配置文件
2. 在该文件下添加
	log = 'D:/mysql.log'
3. 重启mysql服务即可
~~~















































