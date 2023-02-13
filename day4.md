# 库表设计

~~~markdown
1. 员工信息
	字段：id、name、salary、age、
2. 用户表
	字段：id、username、password、realname、gender
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

- 作业

~~~markdown
完成EMS项目，强制登陆
如果不在登录状态下，不允许用户访问emplist页面，强制用户前往登陆页面进行登录
~~~

## 七、全局错误视图配置

~~~markdown
# 在项目上线运行时，需要配置错误的页面给用户

请求与响应：
	如果请求发送成功，且服务器返回了响应，前端页面可以得到一个状态码：200
	
	HTTP请求状态码：
		200 - OK
		301/302 - 重定向
		403 forbidden
		404 资源为找到page not fond
		500 服务器错误
~~~

~~~markdown
如果发生了404或者500这种错误，不能让用户看到错误日志
# 解决方案：
    (1) settings.py文件中
        DEBUG = True是调试模式，需要把True改为False，这样就不显示报错页面了
    (2) ALLOWED_HOSTS = ['*']


# 配置一个错误页面
	在templates对应的目录下，新建404.html,500.html，当出现访问错误的时候，django会自动根据错误的状态码找到对应的html文件，返回给客户
~~~

## 八、基于View的事务控制

~~~markdown
银行卡取钱--->读取银行卡信息--->获取要取的金额--->银行卡扣款--->出钱--->退卡 原子操作 要么都成功，要么都失败回滚到原点
~~~

~~~markdown
事务控制的目的是为了保证数据库中，数据的完整性
事务加在哪些操作中：增删改，只要是写操作都要进行事务控制
~~~

在Django中进行事务控制一共有两种方式

- 方式一：给所有的请求(视图函数)都包裹在一个事务中，在视图函数开始的时候事务开始，函数结束的时候事务commit

~~~python
在settings.py文件中的DATABASES中添加：
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'new_ems',
        'PORT':3306,
        'HOST':'localhost',
        'USER':'root',
        'PASSWORD':'123456',
        'ATOMIC_REQUESTS': True,
    }
}
# 弊端：当函数中有try的时候，错误会优先被try捕获并且处理，此时事务检测不到错误，无法回滚数据
def regist_logic(request):
    try:
        username = request.POST.get('username')
        name = request.POST.get('name')
        pwd = request.POST.get('pwd')
        gender = request.POST.get('sex')
    	if t_user.objects.filter(usn=username):
        	return HttpResponse('用户名重复！！！')
    	user = t_user.objects.create(usn=username,rn=name,pwd=pwd,gender=gender)
        if True:
            10/0
            return redirect(show_login)
    except:
        return redirect(show_login)
~~~

- 方式二：在视图函数中通过上下文管理器with transaction.atomic()：来进行事务控制

~~~python
# 在databases中将 ''ATOMIC_REQUESTS': True' 删掉
def regist_logic(request):
    try:
        username = request.POST.get('username')
        name = request.POST.get('name')
        pwd = request.POST.get('pwd')
        gender = request.POST.get('sex')
    	if t_user.objects.filter(usn=username):
        	return HttpResponse('用户名重复！！！')
    	with transaction.atomic():
            user = t_user.objects.create(usn=username,rn=name,pwd=pwd,gender=gender)
            if True:
                10/0
                return redirect(show_login)
    except:
        # 处理错误的代码
        return redirect(show_login)
# 第二种方式进行事务控制，更加灵活，当发生错误的时候，事务会先检测错误，进行回滚，但是事务本身不能处理错误，所以错误最终还是要被try捕获并且处理
~~~

## 九、 反向解析

### 1、 简介

~~~markdown
在django项目中，经常需要获取某个url来使用
# 在前端页面中
    (1) <a href='login'>跳转</a>
    (2) location.href='login'
    (3) <form action='login'></form>
# 在后端程序中
	(1)重定向 redirect('要跳转的页面URL')
	
上述的URL路径的写法都称之为‘硬编码’，一旦urls.py文件中的path访问发生了修改
path('index/',views.index)--->path('index_page',views.index)，此时用到该访问路径的所有地方都需要修改。
~~~

### 2、 反向解析

~~~markdown
为了解决上述问题，Django提供了解决方案：
只需要在path访问路径中，添加一个name参数即可
path('login_logic',views.login_logic,name='login_logic')
~~~

### 3、 具体使用

~~~markdown
# 1. 在视图函数中
def regist_logic(request):
    ...
    return redirect('regist_logic')# 在这里直接写name参数的值即可，反向解析-django会根据name自动解析到name对应的url访问路径
# 2. 在模板文件html中
	<form action="{%url 'login_logic'%}" method="post">xxxx</form>
~~~

# 一、模板

## 1. 在view中硬编码HTML

~~~python
def hello(request):
    return HttpResponse("<h1>你好</h1>")
~~~

~~~markdown
1. 将python代码和html代码放在一起，将来如果要修改html代码，必须对python文件进行修改。一个大型项目，前端页面的设计和后端程序是要分开的，不同人员会做不同的工作。
2. 所以我们需要将前端页面的设计代码与Python代码进行分离，方便维护，将HTML代码从Python代码中抽离出来，放在HTML文件中
~~~

## 2. MTV模式

~~~markdown
1. M:模型：数据库：ORM
2. T:模板：HTML页面
3. V:视图：实现业务逻辑
~~~

## 3. Django的模板查找机制

~~~markdown
1. django默认是在项目根目录下的templates目录下，查找模板文件


2. Django也可以搜索app目录下的templates，查找模板文件
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [os.path.join(BASE_DIR,'templates')] # 在项目根目录下的templates
        ,
        'APP_DIRS': True, # 在app下的templates
    }
~~~

模板文件的管理方式

~~~markdown
1. 方式一：每个app各自斥候，需要在每个app的templates目录下建立自己的子目录，避免命名冲突
具体操作：
	a. 在app目录下，新建一个templates目录，在templates目录下，新建一个与app名相同的子目录，然后在子目录中创建html文件
	b. 使用：render(request,'app子目录名/xxx.html')
2. 方式二：统一放在项目根目录下的templates目录下，且每个app也有自己独立的子目录
	a. 在app目录下，新建一个templates目录，在templates目录下，新建一个与app名相同的子目录，然后在子目录中创建html文件
	b. 使用：render(request,'app子目录名/xxx.html')
~~~

# 二、模板语言

## 1. 模板语言概述

~~~markdown
1. 模板语言：Django内置了自己的模板语言(模板引擎)，第三方的模板语言，比较流行的有jinja2

2. Django官方表示，除非有不得不换的原因，不然推荐使用内置的模板语言。Django Template Language(DTL)

3. 在模板文件中，动态渲染数据，使用的就是DTL
~~~

## 2. 模板传值

~~~markdown
如果要从后端传值给前端
1. 不使用模板文件
	def hello(request):
		name = 'qinhuanhuan'
		return HttpResponse(name) # 此时的name会直接显示在前端页面
2. 使用模板文件
	def hello(request):
		name='季晨光'
		return render(request,'hello.html',{'name':name})
	在前端的页面中，通过{{ name }}方式取值
~~~

~~~markdown
模板的正确使用，一般是用于衔接view和templates，接受view的数据，渲染在html页面中
~~~

# 三、模板变量

在模板文件中，如果想要显示某个数据，使用的是模板变量。{{变量}}，该变量从view中传递来的，render(requesst,'xxx.html',{'key':'value','key2':'value2'})

## 1. 简单的数据

~~~markdown
变量的类型：字符串、整型等

def hello(request):
	return render(request,'hello.html',{'name':'gaotengfei','age':20})
	
<body>
	{{name}},{{age}}
</body>
~~~

## 2. 列表

~~~markdown
def hello(request):
	return render(request,'hello.html',{'name':'gaotengfei','age':20,'numbers':[1,2,3,4,5]})
	
<body>
	{{name}},{{age}},{{numbers.0}}
</body>
~~~

## 3. 字典

~~~python
def show_login(request):
    return render(request,'ems/login.html',{'name':'季晨光',
                                            'age':80,
                                            'sanwei':{'touwei':80,
                                                      'tunwei':100,
                                                      'yaowei':10}})
  

<h1>{{ sanwei.tunwei }}</h1>
~~~

## 4. Model对象/QuerySet对象

~~~python
def show_login(request):
    user = t_user.objects.all()
    print(user)
    return render(request,'ems/login.html',{'name':'季晨光',
                                            'age':80,
                                            'sanwei':{'touwei':80,
                                                      'tunwei':100,
                                                      'yaowei':10},
                                            'user':user})
    
<h1>{{ user.0.usn }}</h1>
~~~

# 四、模板标签

## 1. if/else标签

~~~python
# 基本的语法格式
		{% if 条件表达式 %}
    		....
        {% endif %}
或者：
		{% if 条件表达式 %}
    		....
        {% else %}
        	...
        {% endif %}
或者：
		{% if 条件表达式 %}
    		....
        {% elif 条件表达式 %}
        	...
        {% else 条件表达式 %}
        	...
        {% endif %}
~~~

~~~html
{% if name == '季晨光' %}
	{{ name }}{{ user.0.usn }}
{% elif name == '季晨光2' %}
	{{ name }}{{ user.0.rn }}
{% else %}
	{{ name }}{{ user.0.pwd }}
{% endif %}
~~~

## 2. for标签

~~~markdown
基本语法
		{% for 变量 in 序列/可迭代对象%}
			...
		{% endfor %}
~~~

~~~python
后端：
def show_login(request):
    user = t_user.objects.all()
    print(user)
    return render(request,'ems/login.html',{'name':'季晨光3',
                                            'age':80,
                                            'sanwei':{'touwei':80,
                                                      'tunwei':100,
                                                      'yaowei':10},
                                            'user':user})

<table cellspacing="0" border="1">
    <tr>
        <td>id</td>
        <td>用户名</td>
        <td>密码</td>
        <td>真名</td>
        <td>性别</td>
    </tr>
    {% for i in user %}
        <tr>
            <td>{{ i.id }}</td>
            <td>{{ i.usn }}</td>
            <td>{{ i.pwd }}</td>
            <td>{{ i.rn }}</td>
            <td>{{ i.gender }}</td>
        </tr>
    {% endfor %}
</table>
~~~

- 反向迭代

~~~markdown
<table cellspacing="0" border="1">
    <tr>
        <td>id</td>
        <td>用户名</td>
        <td>密码</td>
        <td>真名</td>
        <td>性别</td>
    </tr>
    {% for i in user %}
        <tr>
            <td>{{ i.id }}</td>
            <td>{{ i.usn }}</td>
            <td>{{ i.pwd }}</td>
            <td>{{ i.rn }}</td>
            <td>{{ i.gender }}</td>
        </tr>
    {% endfor %}
</table>
~~~

- empty空标签

~~~html
{% for i in list %}
    <h1>
        {{ i.rn }}
    </h1>
{% empty %} # 如果遍历的list对象为空/None/不存在的时候都执行empty下的内容
	绝了
{% endfor %}
~~~

- 遍历二维列表

~~~html
{% for i in list %}
    {% for j in i %}
    	<h1>
        	{{ j }}
    	</h1>
    {% endfor %}
{% endfor %}

{% for i,j in user %}
	{{ i }}{{ j }}
{% endfor %}
~~~

- 遍历的索引

~~~markdown
{% for i,j in user %}
    {{ forloop.counter }} #当前遍历波次是第几次，从1开始
    {{ forloop.counter0 }} #当前遍历波次是第几次，从0开始
    {{ forloop.revcounter }} # 反向索引，从1开始
    {{ forloop.revcounter0 }} # 反向索引，从0开始
    {{ forloop.first }} # 当前遍历波次是否是第一次遍历
    {{ forloop.last }}# 当前遍历波次是否是最后一次遍历
{% endfor %}
~~~

# 五、过滤器

~~~python
<h1>{{ name|lower }}</h1> # 转为小写
<h1>{{ name|upper }}</h1># 转为大写
<h1>{{ name|length }}</h1># 长度
<h1>{{ user|length }}</h1># 列表长度
<h1>{{ salary|default:'0' }}</h1> # 默认值
# 加减乘除：DTL 不支持直接的加减乘除，需要下面这一堆组合使用
<h1>{{ age|add:1 }}</h1># 数字+1
<h1>{{ age|add:-1 }}</h1># 数字-1
<h1>{{ age|divisibleby:4 }}</h1> #模运算
<h1>{% widthratio 5 10 100 as result%}</h1> # a/b*c
{{ result }}
~~~

# 六、静态资源

## 1、 静态资源

~~~markdown
除了HTML文件外，web应用还需要一些其他的文件：CSS、JS、IMG、VIDEO...
在django中引入上述的这些文件，为了搭建一个完整的页面
这些静态资源一般不需要做出改变
~~~

## 2、 如何使用静态资源

~~~markdown
# 方式一：
1. 在app的目录下，新建一个'static'文件夹，将所需的静态资源放在目录下
	<img src="/static/img/傻逼.jpg" alt="无法加载">
	<link rel="stylesheet" type="text/css" href="{% static 'css/style.css' %}" />
	<script src="/static/xxx.js"></script>
	注意：django在查找静态资源的时候，默认去每个app的static目录查找，如果找到则显示
	
# 方式二：
	在项目的根目录下新建任意名字的目录作为静态资源目录
	# django并不会在项目的根目录下查找静态资源文件，需要设置告诉django我新增了静态资源的目录
	# 在settings.py里添加配置
	STATICFILES_DIRS = [os.path.join(BASE_DIR,'sb')]
	
# 注意：
	Django的请求分为两大类
		a. 通过url访问视图函数，路径中不带STATIC_URL的，动态请求
			django会根据url匹配对应的视图函数，然后查找对应的方法进行处理
		b. url中携带了STATIC_URL，则Django会默认按照静态资源陌路下，找到对应的文件内容
~~~

# 七、模板继承

## 1、 模板继承概述

~~~markdown
HTML文件继承：
	Base.html
	jicheng.html
	jicheng.html继承了Base.html以后，它会继承Base.html中的所有内容
~~~

~~~python
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    {% block title %}
        <title>我是父模板</title>
    {% endblock %}
</head>
<body>
{% block middle %}
AAA
{% endblock %}
</body>
</html>



{% extends 'base.html' %}

{#重写父模板中的block块#}
{% block title %}
<title>我是子模板</title>

{% endblock %}
~~~







