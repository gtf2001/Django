# 实用扩展

## 一、验证码

### 1、 简介

~~~markdown
1. 为什么要使用验证码：
	验证码：人类行为验证
	在form中常用的一个组件，目的是为了更好的保障请求的合法性，防止恶意、无效的访问、恶意注册、暴力破解
	
2. 验证码的实现原理：
	在服务器端生成一个随机的码："ABCD"
	将随机码添加到一张图片中，并且添加噪点
	再把图片传到客户端并且显示在页面上
	
3. 第三方库：pillow
	pip install pillow
~~~

### 2、 使用步骤

验证码本质上是生成一个带有随机码的图片，将这个图片在form表单中显示即可

- 从验证码的原始文件中，将captcha的包导入到自己的项目的app目录下

## 二、文件上传

### 1. 简介

~~~markdown
1. 在django中如果要实现文件或者图片的上传，直接使用FileField和ImageField字段即可，而ImageField是继承于FileField.

2. 如果要给某一个用户上传一个头像：
		class t_user(models.Model):
        usn = models.CharField(max_length=20)
        pwd = models.CharField(max_length=20)
        rn = models.CharField(max_length=20)
        gender = models.CharField(max_length=20)
        picture = models.ImageField(upload_to='pics')
        class Meta:
            db_table = 't_user'
            
3. 在使用ImageField之前，需要安装一个第三方模块：pillow

4. 在数据库中，并不会存图片/文件本身，存储的是图片/文件的路径---字符串

5. 在settings.py文件中，配置头像/文件的存储位置
		MEDIA_ROOT = os.path.join(BASE_DIR,'media')
~~~

### 2、案例：为用户上传头像

- 配置头像的存储位置并且安装pillow

~~~markdown
MEDIA_ROOT = os.path.join(BASE_DIR, 'ems/../media')

pip install pillow
~~~

- 定义model类

~~~python
class t_emp(models.Model):
    name = models.CharField(max_length=20)
    salary = models.FloatField(max_length=20)
    age = models.IntegerField(max_length=4)
    img = models.ImageField()
    class Meta:
        db_table = 't_emp'

        #千万记得要执行迁移操作
~~~

- 定义form表单

~~~html
<form action="{% url 'addEmp_logic' %}" method="post" ENCTYPE="multipart/form-data">
{% csrf_token %}
<table cellpadding="0" cellspacing="0" border="0"class="form_table">
<tr><td valign="middle" align="right">name:</td>
	<td valign="middle" align="left">
		<input type="text" class="inputgri" name="name" />
	</td>
</tr>
    <tr>
        <td valign="middle" align="right">salary:</td>
        <td valign="middle" align="left"><input type="text" class="inputgri" name="salary" /></td>
    </tr>
    <tr>
        <td valign="middle" align="right">age:</td>
        <td valign="middle" align="left">
        <input type="text" class="inputgri" name="age" />
        </td>
    </tr>
    <tr>
        <td valign="middle" align="right">头像:</td>
        <td valign="middle" align="left">
        <input type="file" name="head_picture" />
        </td>
    </tr>
    </table>
    <p>
        <input type="submit" class="button" value="Confirm" />
    </p>
</form>
~~~

- 定义view视图函数

~~~python
def addEmp_logic(request):
    name = request.POST.get('name')
    age = request.POST.get('age')
    salary = request.POST.get('salary')
    head_pic = request.FILES.get('head_picture')
    print(name,age,salary,head_pic)
    if name and age and head_pic and salary:
        print('hahaha')
        with transaction.atomic():
            t_emp.objects.create(name=name,salary=salary, age=age,img=head_pic)
            return redirect(show_emplist)
    return HttpResponse('输入不合法')
~~~

- 头像回显

  - 设置静态资源查找目录

  ~~~python
  # 静态资源的查找目录
  STATICFILES_DIRS=[os.path.join(BASE_DIR,'static'),MEDIA_ROOT]
  ~~~

  - 在模板文件中，设置img标签

  ~~~html
  <img src="/static/{{user.img}}" alt='长太丑' height="50px"
  ~~~

## 三、分页显示

### 1、 简介

~~~markdown
当页面中的数据过多时，需要进行分页显示，在页面上应该有对应的页码，上一页，下一页等功能，在点击页号的时候，跳转到对应的页码。

在django中，提供了一个类Paginator(分页器)，来进行分页操作。
~~~

### 2、 分页器paginator

~~~markdown
1. 第一步：初始化一个分页器对象
	pagtor = Paginator(object_list=要分页的数据的‘列表’-可迭代对象,per_page=每页显示的条数)
~~~

- 分页器对象的属性

~~~markdown
1. pagtor.count:
	获取所有页面的对象总数-object_list中元素的个数
2. pagtor.num_pages：
	获取总页数
3. pagtor.page_range
	返回页面的范围
~~~

- 分页器对象的方法

~~~markdown
pagtor.page(number)
# 调用page方法，获取某一页的页面对象
~~~

- 页面对象

~~~markdown
1. page.object_list
	返回当前页面中所有的数据对象
2. page.number
	返回当前的页码
3. page.paginator
	返回当前页面所属的分页器对象
~~~

- 页面对象的方法

~~~markdown
1. page.has_next()
	当前页面是否有下一页-返回值为bool类型
2. page.has_previous()
	当前页面是否有上一页-返回值bool类型
3. page.has_other_pages()
	是否有其他页，如果它有上一页也有下一页，则为True
4. page.next_page_number()
	返回下一页的页码 page.number+1 如果不存在，则抛出异常
5. page.previous_page_number()
	返回上一页的页码 page.number-1 如果不存在则抛出异常
6. page.start_index()
	返回当前页的起始索引
7. page.end_index()
	返回当前页的结束索引
~~~

~~~python
def show_emplist(request):
    username = request.COOKIES.get('username')
    password = request.COOKIES.get('password')
    page_num = request.GET.get('page_num')
    # pagtor = Paginator(object_list=要分页的数据的‘列表’-可迭代对象,per_page=每页显示的条数)

    if username and password:
        users = t_emp.objects.all()
        pagtor = Paginator(object_list=users, per_page = 3)
        if int(page_num) < 1 or int(page_num) >pagtor.num_pages:
            page_num = 1
        # 如果页码存在
        if page_num:
            # 则返回该页码的数据
            users = pagtor.page(page_num)
        else:
            page_num = 1
            users = pagtor.page(page_num)
        return render(request,'emplist.html',{'users':users,'page_num':page_num,'page_range':range(pagtor.num_pages)})
    else:
        return redirect(show_login)
~~~

~~~html
<div id="content">
					<p id="whereami">
					</p>
					<h1>
						Welcome!
					</h1>
					<table class="table">
						<tr class="table_header">
							<td>头像</td>
                            <td>唉第</td>
							<td>姓名</td>
							<td>薪资</td>
							<td>年龄</td>
							<td>选项</td>
						</tr>
						{% for i in users %}
                            <tr class="row1">
                            <td>
                            <img src="/static/{{ i.img }}" alt="User" width="40px"/>
                            </td>
							<td>{{ i.id }}</td>
							<td>{{ i.name }}</td>
							<td>{{ i.salary }}</td>
							<td>{{ i.age }}</td>
							<td>
								<a href="emplist.html">delete emp</a>&nbsp;<a href="updateEmp.html">update emp</a>
							</td>
						</tr>
                        {% endfor %}
					</table>
                <a href="{% url 'emplist' %}?page_num={{ page_num|add:-1 }}">上一页</a>
                {% for i in page_range %}
                    {% if users.number == i|add:1 %}

                    <a href="{% url 'emplist' %}?page_num={{ i|add:1 }}" style="color: #5494F3">{{ i|add:1 }}</a>
                    {% else %}
                    <a href="{% url 'emplist' %}?page_num={{ i|add:1 }}" style="color: black">{{ i|add:1 }}</a>
                    {% endif %}
                {% endfor %}
                <a href="{% url 'emplist' %}?page_num={{ page_num|add:1 }}">下一页</a>
					<p>
						<input type="button" class="button" value="Add Employee" onclick="location='{% url "addEmp" %}'"/>
					</p>
~~~

## 四、中间件

### 1、 简介

~~~markdown
1. 概念：
	中间件（MiddleWare）用于在http请求到达'views'之前及'views返回响应之后'的期间，django会根据自己的规则在合适的时机执行一些方法。
	
2. 中间件的作用
	常用语抽取views中冗余的代码，如每个页面在访问的时候都需要进行“强制登录”
	如果加了中间件，所有请求都会先经过中间件，中间件先判断登录状态，有了登录状态，请求正常执行（放行），否则，中间件直接重定向到登录页面。
~~~

### 2、 定义中间件

~~~markdown
# 在任意目录(项目根目录、app目录)新建一个py文件
~~~

~~~python
from django.shortcuts import redirect
from django.urls import reverse
from django.utils.deprecation import MiddlewareMixin


class MyMiddleware(MiddlewareMixin):
    def __init__(self,get_response):
        super().__init__(get_response)

    # view处理请求之前执行的函数
    def process_request(self,request):
        pass_url = [reverse('regist'),reverse('login'),reverse('regist_logic'),reverse('login_logic')]
        if request.path in pass_url:
            pass
        else:
            is_login = request.session.get('is_login')
            if is_login:
                pass
            else:
                return redirect(reverse('login'))

    # 在process_request之后，view之前执行
    def process_view(self,request,view_func,view_args,view_kwargs):
        pass

    # view处理请求之后执行的函数,响应之前
    def process_response(self,request,response):
        return response

    # 异常处理函数:view函数中出现异常时执行
    def process_exception(self,request,exception):
        pass
~~~

- 激活中间件

~~~markdown
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
    'ems.mid.MyMiddleware'
]
~~~





































