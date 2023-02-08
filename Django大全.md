# Django框架入门

## 一、Django框架简介

### 1. 概述

~~~markdown
# 1. Django是什么，有什么作用？
Django是一个开源的重量级的web应用框架，由python编写而成，用于web后端的开发。

# 2. web前端+数据库+web后端
比如：略
~~~

### 2. 学习目标

~~~markdown
快速构建简易但是完整的web项目，认识MTV架构，熟悉Django的开发流程
M T V
Model
Template
Views
~~~

## 二、 Django环境的搭建

### 1. 安装Django

~~~markdown
# 安装方式：
pip install django==3.0.6

# 检查
1. pip list
~~~

### 2. 搭建虚拟环境

~~~markdown
# 例如：运行多个项目，但是多个项目要求的django版本不一样
1. 项目一需要2.0.6 pip install==2.0.6
2. 项目二需要3.0.6 pip install==3.0.6
后安装的版本会覆盖先安装的版本，需要来回切换版本，很麻烦

希望在电脑中安装多套运行环境
解决方案：虚拟环境
~~~

#### 虚拟环境工具

~~~markdown
1. 安装 virtualenv
	pip install virtualenv
2. 创建虚拟环境
	cmd进入某个目录，virtualenv 虚拟环境的名字
3. 激活虚拟环境
	进入到虚拟环境的scripts目录下，执行activate
4. 安装Django等第三方包
	pip install django==3.0.6
5. pip list查看安装情况
6. deactivate.bat
~~~

## 三、 创建Django项目

~~~markdown
1. 打开pycharm，选择菜单中的File-new project-Django
2. 修改项目名，并且选择之前创建的虚拟环境的解释器即可
~~~

#### 项目启动方式

~~~markdown
1. 使用命令启动
	python manage.py runserver
	
请求与响应的流程
1. 用户在浏览器中输入：127.0.0.1:8000/hello，回车，实际上是向服务器发送了一个请求
2. 服务器程序(Django程序)会接收到请求，在urls.py中，匹配路径，找到对应的视图函数
3. 视图函数接收到请求之后，返回响应 retrun HttpResponse("响应的内容")
4. 用户可以接收到请求返回的响应
~~~

## 四、 MVC模式

~~~markdown
model view controller
model:业务模型
view：用户视图
controller：控制器
~~~

![MTV](G:\太原理工2019WEB\Django\笔记\.assets\MTV.png)



~~~markdown
1. M:Model 模型 与数据库交互，提供数据
2. T:template 模板 html文件，展示的页面
3. V:views 视图 处理业务逻辑

MTV模式的作用：把数据、业务逻辑和界面分离，解耦合
~~~

### 五、Django创建app

~~~markdown
1. 一个项目往往由很多模块，比如电商项目：用户模块、订单模块、商家模块...
2. 在django中使用app来管理一个模块，一个app就是一个功能模块的集合
3. 将一个网站的多个功能划分为多个app，是一个解耦合的过程，整个项目结构松散，利于管理
~~~

- 创建app

~~~markdown
在pycharm中打开终端：
python manage.py startapp app名字
~~~
# Django视图和URL配置

## 内容回顾

~~~markdown
1. Django：由python编写的开源的重量级的web框架
2. 虚拟环境：虚拟的环境
3. Django项目的创建：
	1. 新建项目
	2. Django
	3. 选择环境
	4. 确认
	默认有一个同项目名的主应用、
		settings：项目的配置信息
		urls：路由
	manage.py：启动项目的一个脚本文件，相当于scrapy的main文件
	templates：前段模板放置的默认目录
	
	创建app：python manage.py startapp app名，会在主应用的同级目录下出现一个app名
	views：视图
	apps：进行app的相关配置
	models：数据库的交互
	urls：路由映射
~~~

## 一、MTV模式

### 1. 概述

MTV模式本质上和MVC是一样的，为了保持各个组件之间的松耦合关系。将数据、业务逻辑、界面进行分离来组织我们的代码。

### 2. View的职责

~~~markdown
1. 负责接收前段发起的请求
		def hello(request):
			xxxx
2. 调度model，对数据库进行操作，处理相对应的业务逻辑
		def hello(request):
			//处理业务
			//连接数据库（增删改查）
3. 响应请求
		return render(request,"模板名称")
~~~

## 二、基本开发流程

~~~markdown
1. 创建项目，创建app，在app的views文件中定义相关的视图函数
2. 视图函数需要响应一个结果。return HttpResponse | return render
3. 为视图定义路由映射
4. 启动项目，访问view
~~~

### 1. 启动服务的方式

~~~markdown
1. 在terminal中执行
		python manage.py runserver
	该方式启动，只能允许本机访问：127.0.0.1::8000
2. 也可以修改默认的端口号
	python manage.py runserver 8888
3. 远程访问：
	如果想让其他电脑上的浏览器来访问某一个电脑的django服务器的话，需要以下方式启动
	1. 写一个固定的ip
			# python manage.py runserver 本机ip:端口号
	2. 需要修改settings.py中：
			# ALLOWED_HOSTS = ['*']
	3. 写通用地址：
			# python manage.py runserver 0.0.0.0:端口号
			# ALLOWED_HOSTS = ['*']
~~~

### 2. 完整的url组成

~~~markdown
协议+IP+port+path
1. IP：主机的IP，运行着服务器程序的计算机或者服务器的IP
2. port 端口号：Django服务器默认的端口号为8000，用于区分当前电脑上运行的其他的服务器
3. path 请求的path：定义在urls.py文件中的路由
~~~

## 三、 URL配置

~~~markdown
url的配置定义在urls.py里面
# url配置本质上是url的访问路径和views视图函数的一种关系（对应关系/映射关系）

urlpatterns = [
    path('admin/', admin.site.urls),
    path('hello/', hello),
    path('bye/', views.bye),
]


def hello(requests):
    if requests.META['REMOTE_ADDR'] in ['10.13.1.53','10.13.1.46','10.13.1.109']:
        return render(requests,'')
    return HttpResponse('<h1>hello</h1>')

def bye(requests):
    return HttpResponse('<h1>bye</h1>')
~~~

#### 正则配置

~~~python
# 假如当当网 100万本书
    path('dangdang/1/'),
    path('dangdang/2/'),
    path('dangdang/3/'),
# 解决方案
	re_path("dangdang/\d+/",url_views.book)
~~~

#### URL的管理技巧

~~~markdown
# 之前：
	所有的app的视图函数的对应的访问路径都写在一个urls.py文件中，如果视图函数变多，访问路径也会变多，将来项目的url访问路径的维护会变得非常困难
	
# 现在：
	我们希望，每个app不仅是逻辑上（view.py）独立，同时也希望url访问路径也是独立的，而不是所有都放在一起
	
# 如何做：
	1. 在每个app目录下，都新建一个urls.py文件，在里面写上当前的app的视图函数对应的访问路径
	2. 在全局urls.py文件中，包含以下自己的app的urls
~~~

## 四、命名路径

命名路径指的是在路径中以任意名称去配置指定的参数的形式

### 1. 基本命名路径

~~~markdown
1. # 在url中添加命名空间name和age，被当做参数进行传递
path('hello/<name>/<age>/', hello),

# 在url对应的视图中，需要以关键字参数的形式接收命名空间中的参数，必须与url中的参数名保持一致。

2. 
            def hello(requests,name,age):
                print(name,age)
                if requests.META['REMOTE_ADDR'] in ['10.13.1.53','10.13.1.46','10.13.1.109']:
                    return render(requests,'')
                return HttpResponse('<h1>hello</h1>')
3. 访问：
		http://10.13.1.59:8888/hello/hello/aaa/bbb/
以上操作，可以在视图函数中，获取url路径中的参数值
~~~

### 2. 命名路径转换器

命名路径上传的参数的类型全都是str类型，如果想在接收的时候指定参数的类型，可以在url命名空间中添加转换方式

~~~markdown
本质上是将url访问路径中的字符串转为int类型

浏览器中：http://127.0.0.1:8000/hello/hello/yfsb/70

其中，输入的70本质上是一个字符串
path('hello/<name>/<int:age>/', hello),
可以变成int
~~~

~~~markdown
1. int：接收整数
2. str：接收字符串（默认）
3. slug：字母、数字、下划线、杠
~~~

## 五、 请求处理

~~~markdown
1. 请求与响应的流程
		1. 客户端向服务器发送了一个请求
		2. 服务器接收到请求
		3. 服务器处理请求并且返回响应
~~~

### 1. 发送请求的方式

用户在前端发送请求有三种方式：

1. 

```
<a href="http://10.13.1.59:8888/hello/hello/aaa/123/">a标签</a>
```

2. 

~~~markdown
<form action='url'></form>
~~~

3. 

~~~markdown
location.href
~~~

~~~html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script type="text/javascript">
        function f() {
            location.href = 'http://10.13.1.59:8888/hello/hello/aaa/123'
        }
    </script>
</head>
<body>
    <form action='http://10.13.1.59:8888/hello/hello/aaa/123'>
        <input type="submit" value="提交">
    </form>

    <button onclick="f()">跳转</button>
</body>
</html>
~~~

~~~markdown
以上三种方式，都有一个共同的特点
# a标签、location.href、form表单，必须指定url作为访问的地址
# 在Django中，一个url对应的是一个视图函数
# 视图函数的作用就是接收请求、接收参数、处理请求、返回响应
# 所以a标签，href标签，form，都是最终将请求发送给了视图函数
~~~

- 参数传递方式一：看上面
- 参数传递方式二：

​	可以使用视图函数中的request对象来完成参数的接收，前段必须使用?拼接的形式来完成参数传递

~~~markdown
# 1. 前段请求传递参数的方式：必须以?的形式在url后面拼接，如果有多个参数用&链接
    <form action='http://10.13.1.59:8888/hello/hello/aaa/123?name=yuanfei&nickname=sb'>
# 2. 视图函数接收
	GET方法
	request.GET['key']
~~~

- 接收post请求

~~~markdown
1. form表单发送post请求
		a. 前端页面
            <form action="#" method="post">
                {% csrf_token %}
            </form>
        b. 后端程序：
        	def xx(request):
        		request.POST['key'] # 如果key不存在则报错
        		reqeust.get("key") # 如果key不存在，则为None
        		
# a标签和location.href标签是不可以发送post请求的
~~~

### 2. GET请求和POST请求的区别

~~~markdown
1. POST请求的安全性高于GET，GET请求直接把参数暴露在URL上，POST请求将参数隐藏
		传递的数据需要加密的时候，使用POST请求
2. GET对数据长度有限制，URL的最大长度2048个字符，POST无限制
		传递大量数据的时候，使用POST请求
3. HTPP语义中，规定GET表示获取数据，POST表示提交数据
4. GET只允许传递ASCII字符，而POST可以传递各种数据
5. GET请求的数据可以做缓存，而POST不可以（了解）
~~~

## 练习

~~~markdown
把那个表，做成前后端交互的
~~~


模型02
MySQL
数据库的相关概念
SQL命令
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
mysql：是一款数据库产品。数据库用来对数据进行存储和管理(增删改查)
数据库简称：DB
常见的DB：mysql/oracle/sqlserver/mongodb/redis
数据库存储数据：
支持大量数据、效率高
支持并发
错误检查能力强
数据相对安全
表(table)：存储数据
行(row)：代表表中的一条数据，对应的是一个model类对象。
列(column)：代表数据中的一项内容，字段，键key
主键PK(primary key)：唯一标识表中的一条数据，特点唯一、非空
外键FK(foreign key)：标识当前表和其他表的关系
SQL(structure query language)命令：结构化的查询语言，对关系型数据库中的数据增删改查操作。
1. 简单查询
2. 排序
3. 条件查询
1. 查询工号、姓、名、年薪
select job_number,firstname,lastname,salary from emplyees;
2. 查询所有列
select * from emplyees;
select job_number,firstname,lastname,salary,married from emplyees;# 效率高
3. 对列进行计算
select job_number,firstname,lastname,salary,salary*12 from emplyees;
4. 起别名
select job_number,firstname,lastname,salary,salary*12 as 年薪 from
emplyees;
5. 字符串拼接
select job_number,CONCAT(firstname,"·",lastname) as 姓名,salary,salary*12
as 年薪 from emplyees;
语法：select 字段名1,字段名2..... from 表名 order by 字段名 asc(默认升序)|desc(降序),字
段名2 asc|desc
# 		根据薪资进行降序排序，如果薪资相同，则按照结婚次数排序
select * from emplyees ORDER BY salary desc , married desc;
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
4. 函数
1. 单行函数
2. 组函数
5. group by 分组
6. having语句
7. 表连接
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
概念：函数作用于表中的一组数据，有一组数据就执行一次，如果没有对表进行分组操作那么这整张表就属于
同一组
max()
min()
sum()
avg()
语法：select ... from ... where ... group by ...
统计各个公寓员工的人数：1. 公寓相同则归为一组
select partment,count(salary),lastname from emplyees GROUP BY partment;
having:对分组后的结果进行过滤，符合条件的留下来
语法：select ... from ... where ... group by ... having ... order by...
每个部门的平均薪资，只显示平均薪资高于1W的
select partment,AVG(salary) 平均薪资 from emplyees GROUP BY partment HAVING 平
均薪资 > 10;
where：对分组前的数据进行过滤：效率高
having：先分组，后过滤
总结：如果过滤条件可以用where也可以用having，先使用where
过滤条件中使用分组后的数据，必须使用having
1. 内连接
2. 外连接
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
语法：select ... from 表1 [inner] join 表2 on 连接条件 where ... group by ... having
... order by
- 查一个人的工号，姓，名，工资，所在部门的编号，所在部门的名称
select job_number,firstname,lastname,salary,department_name from emplyees INNER
JOIN department on emplyees.partment = department.department_id;
内连接特点：
1. 查询结果：左表和右表基于连接条件，匹配成功的数据作为查询后的总行数
2. 必须有连接条件
3. 左表和右表没有顺序要求
注意：如果存在字段名重复，必须用表名.字段名或者表别名.字段名进行区分
语法：select ... from 表1 left|right|full join 表2 on 连接条件 where ... group by
... having ... order by
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
3. 自连接
4. 多表链接
8. 建表语句
自连接：是一种特殊的表链接，通常选用内连接的语法
特殊：参与表链接的2张表或多张表都是同一张表
id category_name level parent_id
1 后端语言 1 null
2 前端语言 1 null
3 java 2 1
4 c++ 2 1
5 html 2 2
- 查询出所有二级类别对应的一级类别
先做表连接然后进行过滤
select * from category c1 left join category c2 on c1.parent_id = c2.id where
c1.`level`=2;
先过滤再表连接
select * from (select * from category where level=2) c1 left join category c2 on
c1.parent_id = c2.id;
select ... from 表1 join 表2 on 连接条件 join 表3 on 连接条件 ...
多个left join即可
DDL:数据定义语言
create table 表名(
字段名1 数据类型 [默认值][约束][约束],
字段名2 数据类型,
字段名3 数据类型---最后一个字段不加逗号
)
数据类型：
int类型：整数，占用内存4字节
double类型：代表小数，占用8字节，使用方式：double(5,2)第一个参数表示整体多少位，第二个参
数表示小数点后多少位
char(n)：固定长度字符串类型(n最大是2000)
varchar(n)：可变长度字符串类型(n最大是4000)，最多分配n字节
char(5) varchar(5)
'abc' 'abc'
占空间 省空间
效率高 效率低
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
事务
一、 关联关系
1. 概述
2. 关联关系的种类
3. 非空约束
可以重复，不能为空
not null
4. 外键
该字段的值不能随便输入，必须是另一张表的主键或者唯一键存在的值
可以重复，可以为null
references
表：学生
id name class_id
1 qd 1
2 wt 2
3 dsh 1
4 qhh 5---错误，必须是班级表中主键的值
表：class
id name
1 2021
2 2022
概念：一个业务1~n条sql组成。为了保证业务的正确性，多条sql要么一起成功，要么一起失败
1. 回滚段
数据库会为每一个连接上的客户端，开辟一小块空间(回滚段)，用于暂时存储sql语句的执行结果，当事
务commit时，会把自己回滚段中的数据真正写入表，当事务rollback的时候，会清空自己回滚段中的数据。
回滚段的特点：只要是写入操作，都会进入到回滚段中，写入操作：增删改
2. 锁：
在一个事务中insert/update/delete 数据时，会获取改这条数据的标记，直到该事务结束
(commit/rollback)，才会释放锁标记。其他客户端才能获取锁标记进行操作。
注意：查询操作没有事务。
3. 事务的四大特性：
ACID
A：Atomic(原子性):事务中的多个sql为一个整体，都成功则提交，有一个失败则寄
C：Consistency(一致性):事务结束后 和数据库中的数据状态必须一致
I：Isolation(隔离性):多用户并发，用户和用户的数据是相互不影响的
D：Durability(持久性):事务对数据的影响是持久的、深远的，不是临时的
关联关系指的是数据表之间的数据是相互依赖和影响的，表之间有从属关系。比如：有一个用户表，用户又有
一个订单表，用户表和订单表就有从属关系。
3. Model类中的关联关系
五、 懒加载
1. 一对一
一个人一本护照
2. 一对多
一个部门有多个员工
3. 多对多
一个学生有多门课，一门课有多个学生
当Django的ORM进行数据查询的时候，QuerySet不会立即执行sql去查询数据，而是在数据被使用到的时
候，才会查询。
延迟执行
ps = Person.objects.all() # 查询所有人的信息，但是实际上这个时候并没有执行select
print(ps)
# mysql添加日志
1. mysql安装目录下有个my.ini---配置文件
2. 在该文件下添加
log = 'D:/mysql.log'
3. 重启mysql服务即可





















































