# 模型

## 一、MTV

~~~markdown
1. M：Model：模型：模型是数据的唯一可靠的来源，实际上要与数据库交互

2. Template：模板：使用HTML来给用户展示界面

3. Views：视图（函数）：负责接收请求处理业务逻辑，视图也是数据库和模板的中间人
~~~

## 二、ORM机制

~~~markdown
ORM：
	Object Relational Mapping-对象关系映射（面向对象和关系型数据库之间的一种映射关系）
核心：
	使用面向对象的方式与数据库进行交互
~~~

- 对比

~~~markdown
未使用ORM：
	import MySQLdb
	conn = MySQLdb.connect(xxxx)
	cursor.execute(insert/select)# 使用原生的SQL语句
	...
	conn.close()
	
使用ORM之后：
	类和对象
	class User(model.Model):
		username = xxx
		password = xxx
	在ORM中，这样的一个类，对应的是数据库中的一个表
	存储数据：user = User(username,password)   user.save() # 将数据存储到数据库的表中
~~~

- 优势(了解）

~~~markdown
1. 使用面向对象的方式更符合程序员的思维方式
2. 使用ORM的方式操作数据库，更加的简单和方便
3. 使用ORM的程序员不用关心底层使用的是MySQL、Oracel、SQLite等，更换数据库，ORM会自动进行转换
~~~

## 三、 Model层的开发过程

### 基本开发流程

~~~markdown
1. 安装数据库驱动
	pip install mysqlclient
2. 配置数据库settings.py
		DATABASES = {
            'default': {
                'ENGINE': 'django.db.backends.mysql',# 修改为mysql
                'NAME': 'django_mysql',# 库名
                'HOST': '127.0.0.1',# 主机名/IP
                'PORT': 3306,#端口号
                'USER': 'root',#用户名
                'PASSWORD': '123456'# 密码
            }
        }
	
问题1
        # 如果出现了版本不兼容的问题，则添加以下配置
        from django.db.backends.mysql.base import DatabaseWrapper
		DatabaseWrapper.data_types['DateTimeField'] = "DateTime"
问题2
	#
	# 'DIRS': [BASE_DIR / 'templates']
代替
	'DIRS': [Path(BASE_DIR, 'templates')]
或
	'DIRS': [os.path.join(BASE_DIR,'templates')]

3. 创建一个app，并且挂载app
	python manage.py startapp modelapp
	在settings.py中，INSTALLED_APPS = [XXXXX,'modelapp']
4. 打开models.py，定义models类
        # 这里定义的model将来会对应着数据库中的一张表
        class User(models.Model):
            username = models.CharField(max_length=20)
            password = models.CharField(max_length=20)
	    class Meta:
        	db_table = 'users'   # 定义表名
5. 执行迁移操作
		python manage.py makemigrations modelapp # 生成一个记录文件，此时模型并没有移植到数据库中
		python .\manage.py migrate
		
可能会出现要求mysql版本5.7，需要将Django降级为3.0.6

6. 配置URL路径
在根url.py中导入include
	from django.urls import path,include
在path中增加
    	path('emsapp/', include('emsapp.urls'))
在app目录中自行创建一个urls.py
	from django.urls import path
	from emsapp import views
	urlpatterns = [
		...  # 配置URL
    		path('addEmp/',views.show_addEmp),
		...
	]
	
7. settings.py中
	ALLOWED_HOSTS = ['*']	 #允许访问ip
	STATIC_URL = '/static/'   #静态img/css等路径，在app中创建static文件夹

~~~

~~~markdown
# 总结：
	ORM对象关系映射：
		1. 一个model类对应数据库中的一张表
		2. 类中的属性对应表中的字段
		3. 一个model类的对象对应的是表中的一行数据
操作一行数据，就是操作一个model对象，要操作一个字段就是操作对象的属性，如果要操作表就是操作model本身
~~~

## 四、模型字段

#### 1. 字段类型

| 字段类型                               | 描述                                                         |
| -------------------------------------- | ------------------------------------------------------------ |
| AutoField                              | 一般不建议使用，默认为主键类型，不需要自己定义               |
| SmallIntegerField                      | 数据库中的：smallint 2字节<br />-32768~32767                 |
| IntegerField                           | 数据库中的：int 4字节<br />-2147483648~2147483647            |
| BigInteGerField                        | 数据库中的：bigint 8字节                                     |
| FloatField                             | 数据库中的：double                                           |
| Decimal(max_digits=5,decimal_places=2) | 数据库中的：decimal(5,2)<br />数字最大位数为5，小数点后的位数为2<br />-999.99~999.99 |
| CharField(max_length=20)               | 数据库中的：varchar(20)                                      |
| TextField                              | 数据库中的：longText                                         |
| BooleanField                           | 数据库中的：tinyint(1和0)                                    |
| NullBooleanField                       | 数据库中的：tinyint(1/0/null)                                |
| DateField                              | 数据库中的：date                                             |
| TimeField                              | 数据库中的：time                                             |
| DateTimeField                          | 数据库中的datetime<br />可选参数：auto_now=True表示更新时间为当前时间<br />auto_now_add=True添加为第一次添加该数据的时间 |
| OneToOneField                          | 一对一                                                       |
| ForeignKey                             | 外键字段、一对多                                             |
| ManyToManyField                        | 多对多                                                       |

#### 2、 字段参数

~~~markdown
1. null 默认为flase
	name = models.CharField(max_length=20,null=False) # 字段不允许为空
	name = models.CharField(max_length=20,null=True) # 字段允许为空
2. default 定义默认值
	name = models.CharField(max_length=20,default='许财')
3. primary_key 定义主键（不常用）
	id = models.AutoField(primary_key=True) # 一般不自己定义
4. unique 唯一
	name = models.CharField(max_length=20,unique=True) # 当前列的值是唯一的
5. db_column 定义字段的别名(不常用)
	gender = models.BooleanField(db_column='xb')#一般不建议修改别名
6. db_index 定义索引
	age = models.SmallIntegerField(db_index=True) # age这一列就有了索引
	# select * from emplyee where age=18;
~~~

#### 3. Meta元数据

~~~markdown
# 元数据类：可以指定生成的表名、排序方式、联合约束等等
1. db_table = 'Usertable' # 定义表名
2. ordering=['password','username'] # 定义列名(必须按照上面定义的列名来写)

		class User(models.Model):
            username = models.CharField(max_length=20)/
            password = models.CharField(max_length=20)
            class Meta:
                db_table = 'Usertable' # 定义表名
                ordering=['password','username'] # 定义列名(必须按照上面定义的列名来写)
~~~

## 五、增删改操作

#### 1. 增加数据

~~~markdown
# 方式一：先创建对象，调用save方法
	user = User(username='Tom',password='123456')
    user.save()
# 方式二：直接使用类.objects.create()
	User.objects.create(username='ject',password='123456')
~~~

#### 2. 删除数据

~~~markdown
# 1. 先获取数据
	user = User.objects.get(id='1')
    print(user)
# 2. 调用delete方法
	user.delete()
	
# 删除所有数据
	user = User.objects.all()
    user.delete()
~~~

#### 3. 修改数据

~~~markdown
# 1. 获取数据
user = User.objects.get(id=1)
# 2. 修改数据-实际上是修改了对象的属性
user.password = "i'm a big yuanpi"
# 3. 将修改过的对象保存到数据库
user.save()
~~~

## 六、查询操作

### 1. QuerySet类

~~~markdown
1. 每一个model类，都有一个objects属性 (类名.objects)，该属性的类型
		user = User.objects
        print(user)
        print(type(user))
        # 类型为---><class 'django.db.models.manager.Manager'>
2. 对对象的检索(查询)，都通过Manager对象来构造了一个QuerySet对象来实现
		user = User.objects.get(id=1)
		Mnager调用了它的get方法实现了查询，实际情况是Manager构造了一个QuerySet对象来实现查询
3. Manager对象和QuerySet对象
		Manager是一个管理者，不是真正的执行者
		QuerySet是实际执行查询操作的人
4. User.objects.all()
		是Manager调用了all()方法，但是它的内部没有直接执行查询操作，而是让QuerySet调用了all()方法执行的查询操作
5. all()的返回值
		QuerySet对象---查询集；它本身可以执行查询操作，也可以存放查询结果
~~~

### 2. 查询方法

~~~markdown
所有的查询方法、查询动作全部都是基于QuerySet的
Manager的任务是管理QuerySet，在有需要的时候对QuerySet进行调用
~~~

#### 2.1 基本的查询方法

~~~markdown
1. all()方法
		用法：User.objects.all()
		作用：查询全部数据
		返回值：QuerySet对象-类似于一个列表的对象
2. get()方法
		用法：User.objects.get(属性名=值)
		作用：查询一条数据：绝不能多也绝不能少
		返回值：Models对象
3. filter()方法
		用法：User.objects.filter(属性名=值)
		作用：可以查询空或者1条数据或者多个数据(介于get和all之间)
		返回值：QuerySet对象-如果没有查询到结果返回一个空的QuerySet对象
4. count()方法
		用法：User.objects.count()
		作用：查询结果的数量
		返回值：int类型
5. first()方法和last()方法：
		用法：User.objects.first()/User.objects.last()
		作用：获取表中的第一条或者最后一条数据
		返回值：model对象
6. exclude()方法：
		用法：User.objects.exclude(属性名=值)
		作用：查询相反的结果
		返回值：QuerySet对象
~~~

#### 2.2 排序方法

~~~markdown
# 默认按升序排列
users = User.objects.order_by('id')
# 降序排列
users = User.objects.order_by('-id')
# 先按年龄排序，年龄相同的时候按姓名排序
users = User.objects.order_by('age','name')
~~~

#### 补：

~~~markdown
# 取QuerySet的一部分数据
QuerySet的切片和列表一样，但是不支持负数下标
~~~

#### 2.3 条件查询

~~~markdown
1. 在众多的查询方法中，可以使用get，filter，exclude，但是他们更适合于做等值查询
2. 如果想要做比较运算> < <= >=，就需要修改上述方法的参数
		解决：改符号【lt gt lte gte】
		users = User.objects.filter(id__gt=1)# 查询id>1
		
		users = User.objects.filter(id__lt=1)# 查询id<1
		
		users = User.objects.filter(id__lte=1)# 查询id<=1
~~~

#### 2.4 模糊查询

~~~markdown
# select * from t_user where name like '%z%'
1. contains/icontains 包含/包含(对大小写不敏感)
2. startswith/istartswith 以...开头
3. endswith/iendswith 以...结尾

eg:
		User.objects.filter(name__contains='w')
~~~

#### 2.5 范围查询

~~~markdown
1. in    在某个集合中
2. range 在某个范围中

eg:
	users = User.objects.filter(id__in=(1,3))
	users = User.objects.filter(id__range=(1,3))
~~~

#### 2.6 空值查询

~~~markdown
# 查询没有做青年大学习的用户
users = User.objects.filter(status=None)

users = User.objects.filter(status__isnull=True)

# 查询已经做了青年大学习的用户
users = User.objects.filter(status__isnull=False)
~~~

#### 2.7 日期查询-了解

~~~markdown
year/month/day/hour/minute/second/week(一年中的第几周)/week_day(星期几)
# 查询生日为2019的用户
users = User.objects.filter(birthday__year=2019)
users = User.objects.filter(birthday__day=20)
~~~

#### 2.8 映射查询

~~~markdown
查询部分列
# select * from t_user;
# select name,age from t_user;

users = User.objects.all() # 查询所有行所有列
users = User.objects.get(id=1) # 查询id为1的这一行的所有列
~~~

- values(列名1,列名2,...)

~~~markdown
users = User.objects.values('username','password')# 返回值为QuerySet对象，但是元素为字典
~~~

- only(列名1,列名2,...)

~~~markdown
# 率先查询username和password，其他的列有需要的时候才查询，不需要则不查询。懒加载。
users = User.objects.only('username','password')
~~~

#### 2.9 聚合函数

~~~markdown
# Max()/Min()/Sum()/Avg()/Count()
from django.db.models import Max,Min,Sum,Avg,Count
users = User.objects.aggregate(Avg('id')) # 返回一个字典
users = User.objects.aggregate(avg = Avg('id'),max = Max('id'),min=Min('id')) # 返回一个字典
~~~

#### 2.10 分组统计

~~~markdown
语法：
	User.objects.values('age').annotate(Max('salary'))
~~~

~~~markdown
User.objects.all().values('age').annotate(Max('salary')).order_by('age')
~~~

#### 2.11 F()和Q()函数

- F()函数

~~~markdown
需求：查询id大于年龄的用户
User.objects.filter(id__gt=age)# 错误写法
User.objects.filter(id__gt=F('age'))

# 什么时候需要用F()函数：
	当查询条件中需要使用到另外的列(属性的时候)
	
# eg:
	# blog    create_time   edit_time
	Blog.objects.filter(edit_time__gt=F('creat_time'))
~~~

- Q()函数

~~~markdown
User.objects.filter(id__gt=2,age__gt=18) # 查询id大于2并且年龄大于18的用户

# 查询id大于2或者年龄大于18 的用户
users = User.objects.filter(Q(id__gt=2)|Q(age__gt=18))

# 表示非  ~
users = User.objects.filter(Q(id__gt=2)|~Q(age__gt=18))
~~~

## 七、Raw-SQL

~~~markdown
Raw-SQL:原生SQL
# 如果上述的方法不能满足业务需求的时候，可以使用raw函数
users = User.objects.raw('select * from usertable')
~~~

















