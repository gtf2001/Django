# 关联关系

## 1. 概述

~~~markdown
关联关系指的是数据表之间的数据的相互依赖和影响，表之间有从属关系。比如：有一个用户表，用户又有一个订单表，用户表和订单表之间有从属关系

# 部门表
	部门id	部门名称	部门职责
	1		研发部			写代码
	2		测试部			让研发部重新写
	
# 员工表
	员工id	员工姓名	年龄		薪水		所在部门的id
	1			qyh		40		2000			1

# 员工表和部门表之间存在关联关系，MySQL中通过外键来搭建（加强）两个表之间的关联关系
~~~

## 2. 关联关系的种类

~~~markdown
1. 一对一
	一个人只能有一个身份证号
2. 一对多
	一个班级可能有多个学生
3. 多对多
	一个学生可以选多门课，每门课都可以有多个学生
~~~

## 3. Model类中的关联关系

~~~markdown
在ORM中搭建关联关系-使用外键
在ORM中，所有表都是通过Model类来产生的，如果要表和表之间有关联关系，此时需要让Model类之间产生个UAN
class Department(models.Model):
	id = models.CharField(max_length=20,primary_key=True)
    department_name = models.CharField(max_length=20)
    job = models.CharField(max_length=20)
	
class Employee(models.Model):
	id = models.CharField(max_length=20)
    name = models.CharField(max_length=20)
    age = models.CharField(max_length=20)
    salary = models.CharField(max_length=20)
    dept_id = models.ForeignKey(to=Department,)
    
# 1. ForeignKey(to=对方的类名,on_delete=models.CASCADE) --- 一对多

# 2. OneToOneField(to=对方的类名,on_delete=models.CASCADE) --- 一对一

# 3. ManyToManyField(to=对方的类名) --- 多对多没有删除选项
~~~

# 请求与响应

## 一、请求与响应的流程



## 二、 HttpRequest对象

~~~markdown
1. 当用户向服务器发起一个请求的时候，Django会自动创建一个HttpRequest对象，并且将请求的参数包装在该对象的GET或者POST属性中，然后将该对象作为参数传给视图函数的形参request

2. HTTP请求：
	HTTP：超文本传输协议，前端页面和后端程序之间通信的一种规范。
	从前端页面发送一个请求就称为HTTP请求
	
3. Django的执行过程
	(1)创建一个对象-HttpRequest
		req = HttpRuest()
		req.GET['name'] = 'xx' # 给字典增加了一个键值对
		req.GET['age'] = 18 # 又加了一个键值对
~~~

- HttpRequest对象常用的属性

~~~markdown
1. GET和POST - 类似于字典，包含了GET请求和POST请求传递时所携带的参数
	request.GET.get('key')
	request.POST.get('key')
2. method属性 - 表示请求的方法
	def hello(request):
		if request.method == 'GET':
			request.GET.get('key')
		elif request.method == 'POST':
			request.POST.get('key')
3. COOKIES - 后续讲解
~~~

## 三、 HttpResponse对象

### 1. Response对象简介

~~~~markdown
1. HttpResponse是一个响应对象，它是由开发者自己创建的，每一个视图函数都必须返回一个HttpResponse对象
2. request对象和Response实现了客户端和服务端之间的信息传递的作用
	request对象用于接收前端浏览器传递的数据
	response对象负责将服务器的数据返回给前端
~~~~

### 2. 如何创建响应对象

~~~markdown
1. 方式一：不使用模板文件，直接返回字符串
	def hello(request):
		return HttpResponse('')
		
2. 方式二：调用模板
	def login(request):
		return render(request,'login.html') # render的本质还是HttpResponse
~~~

## 四、View的跳转

### 1、 跳转的情景

- 重定向：redirect：可以完成视图与视图之间的跳转

~~~markdown
# 基本跳转：从视图中跳转到Template
	def index(request):
	return render(request,"index.html")

# 从视图跳转到另外一个视图，希望接下来让对应的视图函数完成相关的处理
	通过redirect完成
	比如：用户在发起注册请求，并且完成注册之后，通过redirect重定向到访问登录页面的视图函数中，由该请求完成访问登录页面的功能。
~~~

### 2、 跳转方式

~~~markdown
1. 转发：基本的跳转，浏览器中URL不变
	转发是一个请求内的跳转，用于view到template之间的跳转，render
2. 重定向：从view跳转到view，浏览器的URL会发生改变
	重定向用于view与view之间的跳转
	redirect(to=要跳转的页面的URL)
~~~

## 五、Cookie

### 1、 Cookie简介

~~~markdown
1. Cookie实际上是一种数据存储技术，由服务器产生，并且保存在浏览器中的一种技术，储存在用户客户端上的数据
2. 由于HTTP协议是一种无状态的协议，客户端浏览器和服务端交换完毕之后，再次交换数据需要建立新的连接

比如：登录邮箱，7天自动登录/记住我
~~~

### 2、 Cookie的应用场景

~~~markdown
- 保存登录信息-用户名和密码
- 保存用户的搜索记录
~~~

### 3、Cookie的使用过程

#### 3.1 存储Cookie

- 存储Cookie需要借助Response对象来完成，当通过response对象设置好cookie之后，再将response响应到客户端，cookie就会随之保存到浏览器中

- 不使用模板

~~~python
def index(request):
    resp = HttpResponse('存储cookie')
    resp.set_cookie('username','xx')
    resp.set_cookie('password','123456')
    return resp
~~~

- 使用模板

~~~python
def index(request):
    resp = render(request,'index.html')
    resp.set_cookie('username','xx')
    resp.set_cookie('password','123456')
    return resp
~~~

### 3.2 读取Cookie

~~~markdown
# 读取cookie需要由request对象来完成
# 当向服务器发送一个请求的时候，request对象会携带浏览器中的所有cookie来访问服务器

def show_addEmp(request):
    username = request.COOKIES.get('username')
    password = request.COOKIES.get('password')
    print(username,password)
    return render(request,'templates/ems/addEmp.html')
~~~

### 3.3 Cookie的生命周期

~~~markdown
默认的生命周期：
	一次回话：浏览器打开到关闭，关闭浏览器后，cookie会失效
	在实际使用的时候，往往会给cookie设置一定的存活时间
修改生命周期：
	resp.set_cookie('username','xx',max_age=值/秒)
	
	(1) max_age=0 删除cookie
	(2) max_age=-1 相当于不设置，生命周期为会话周期
	(3) max_age=100 单位是秒，存活100秒
~~~

### 3.4 Cookie的中文问题

~~~markdown
# cookie不能直接存储中文

# 存储
        username = '范子怡'.encodce('utf-8').decode('latin-1')
        resp.set_cookie('username',username,max_age=60*60*24*365*10)
# 读取
		username = request.COOKIES.get('username')
		username = username.encode('latin-1').decode('utf-8')
~~~

~~~markdown
# 总结
1. 用户访问登录页面的时候，需要获得cookie中保存的用户信息
2. 如果用户信息存在，并且和数据库的校验通过，则不需要让用户进行登录，直接跳转项目首页即可，如果cookie中没有用户信息或者用户信息对比失败，则跳转到登录页，让用户重新登录
3. 当用户在登录的时候，如果勾选了记住我，我们需要将用户信息保存在cookie中
4. 如果用户登录的时候没有记住我，则无需存cookie直接访问首页
~~~

## 六、Session

### 1. Session简介

~~~markdown
1. Cookie将数据存在浏览器中，Session会把会话状态存在服务器端。存在数据库中。
2. Session(会话)一般指的是浏览器页面打开到关闭的这段时间。Session技术一般用于多个请求之间共享数据状态。
3. 比如：
	登陆成功以后，也可以使用Session进行记录，声明周期为一个会话周期，然后可以去访问‘我的订单、我的余额’等页面。
~~~

### 2. Session的使用

#### 2.1 启用Session

~~~markdown
1. INSTALLED_APPS中需要有sessions
	INSTALLED_APPS = [...,'django.contrib.sessions',....]

2. MIDDLEWARE = [...,'django.contrib.sessions.middleware.SessionMiddleware',...]

3. 需要执行迁移的命令python manage.py migrate 生成django_session表---存储session数据
~~~

#### 2.2 Session生命周期

~~~markdown
- 默认生命周期
	默认生命周期是2周
	
- 修改：
	一个session存活周期为一次回话(不成文的规定)
	在settings.py文件中添加配置
	SESSION_EXPIRE_AT_BROWSER_CLOSE = True
~~~

### 2.3 Session的存储

~~~python
request.session['username'] = username
request.session['password'] = password
~~~

### 2.4 Session的读取

~~~python
def show_empList(request):
    users = Employee.objects.all()
    username = request.session.get('username')
    password = request.session.get('password')
    print(username,password)
    return render(request,'templates/ems/emplist.html',{'users':users})
~~~

##### 注意

~~~markdown
1. Session的存储和读取都借助于request来完成
2. Session没有中文编码问题
3. Session没有长度限制(储存在数据库中)
4. Session的数据存在服务器，相较于Cookie更安全
~~~

### 2.5 清除Session

~~~markdown
1. request.session.flush()
	清除Session的数据，清除表中的数据及cookie中的session
2. request.session.clear()
	清除的session的键值对，但是session表中的数据，依旧还在
3. del request.session[key]
	删除指定的session中的某一个键值
~~~

### 3. Session的实现原理

~~~markdown
1. 浏览器第一次请求session对象的时候，服务器会创建一个session并且生成一个sessionid，存储数据库中(session_key)，同时将sessionkey返回给浏览器，存储在浏览器的session_id中
2. 在浏览器不关闭的情况下(会话周期)，之后的每次请求都会携带cookie中的sessionid到达服务器，服务器接收到请求之后，可以通过request对象，获取到cookie中的sessionid，再通过该id在数据库中找到对应的session_data给请求者使用

# 不同的用户，打开的是不同的浏览器，去存储session的时候，会给每一个用户生成一个唯一的sessionid，并且把sessionid保存在用户自己的浏览器中
# 用户下一次再去请求session数据的时候，请求会携带浏览器中的cookie(cookie中有一个sessionid)，根据sessionid找到对应的数据
~~~

### 4. session和cookie的区别

~~~markdown
# 1. 储存位置
	session存在服务端中(数据库)，cookie保存在客户端中(浏览器中)。
# 2. 在关闭浏览器后还需要使用的数据使用cookie保存
	例如：记住我
	需要在多个请求之间共享的数据，保存在session中
		共享用户的登录信息
# 3. 每个网站在浏览器中cookie的数据存储上限是4K，而session是4G
# 4. cookie保存数据在浏览器本地，隐私性较低，安全性较低，还有可能因为用户禁用cookie导致功能无法正常使用
# 5. cookie的默认生命周期是一个会话，但是一般会修改一定的周期。session默认是两周，但是一般修改为一个会话，但是由于浏览器限制可能会失败。失败需要手动清除session。
~~~























































