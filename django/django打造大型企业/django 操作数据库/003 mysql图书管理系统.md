#### 实现了一个图书管理系统

1、可以发布图书，同时也可以删除图书以及查看图书详情

控制部分代码如下：

```python
from django.shortcuts import render,redirect,reverse# 渲染 跳转 反转
from django.db import connection  #引入数据库连接
# Create your views here.
def get_cursor():
    #定义了一个获取数据库游标的函数
    return connection.cursor()

def index(request):
    cursor = get_cursor()
    #cursor.execute("select id,name,author from book")
    #books = cursor.fetchall()
    #返回的是[(1,'三国演义,'罗贯中'),(2,)....]
    #cursor.execute("insert into book(id,name,author) values(null,'三国演义','罗贯中')")
    #可以插入数据，但是不能获取数据
    cursor.execute('select * from book')#从book表中，查询所有的数据
    books = cursor.fetchall()#查询所有的结果((id,name,author),(id,name,author)...)
    context = {
        'books':books,
    }
    return render(request,'index.html',context=context)

def add_book(request):
    if request.method =='GET':
        return render(request,'add_book.html')
    else:
        name = request.POST.get("name")
        author = request.POST.get("author")
        cursor = get_cursor()
        cursor.execute("insert into book(id,name,author) values(null,'%s','%s')"% (name,author))
        return redirect(reverse('index'))


def book_detail(request,book_id):
    cursor = get_cursor()
    cursor.execute("select id,name,author from book where id = %s "% book_id)
    book = cursor.fetchone()#查询一条数据
    context = {
        'book':book,
    }
    return render(request,'book_detail.html',context=context)
def delete_book(request):
    if request.method == 'POST':#判断post请求还是get请求 很重要
        book_id = request.POST.get('book_id')
        cursor = get_cursor()
        cursor.execute("delete from book where id=%s "% book_id)
        return redirect(reverse('index'))
    else:
        raise RuntimeError("删除图书的method错误")
```

urls.py的代码如下：

```python
from django.urls import path
from front import views
urlpatterns = [
    path('', views.index,name='index'),
    path('add_book/', views.add_book,name='add_book'),
    #repath('book_detail/(?P<book_id>\d+/))',views.book_detail,name='book_detail')
    path('book_detail/<int:book_id>/',views.book_detail,name='book_detail'),
    path('delete_book/',views.delete_book,name = 'delete_book')
]
```

模板代码如下：

```html
<!--base.html-->
{% load static %}#加载静态页面
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>图书管理系统</title>
    <link rel="stylesheet" href="{% static 'front/base.css' %}">#加载static下面的静态css文件
</head>
<body>
<nav>
    <ul class="nav">
        <li><a href="/">首页</a></li>
        <li><a href="{% url 'add_book' %}">发布图书</a></li>
    </ul>
</nav>
{% block content %}{% endblock %}#定义了一个，在子模板中可以代替内容，继承里面的用{{super()}}
</body>
</html>
<!--index.html-->
{% extends 'base.html' %}
{% block content %}
    <table class="index_table">
        <thead>
            <tr>
                <th>序号</th>
                <th>书名</th>
                <th>作者</th>
            </tr>
        </thead>
    <tbody>
        {% for book in books %}
            <tr>
                <td>{{ forloop.counter }}</td>
                <td><a href="{% url 'book_detail' book_id=book.0 %}">{{ book.1 }}</a></td>
                <td>{{ book.2 }}</td>
            </tr>
        {% endfor %}
    </tbody>
    </table>
{% endblock %}
<!--add_book.html-->
{% extends 'base.html' %}
{% block content %}
    <form  action="" method="post">
        <table>
            <tbody>
                <tr>
                    <td>书名:</td>
                    <td><input type="text" name="name"></td>
                </tr>
                <tr>
                    <td>作者:</td>
                    <td><input type="text" name="author"></td>
                </tr>
            <tr>
                <td></td>
                <td><input type="submit" value="提交"></td>
            </tr>
            </tbody>
        </table>
    </form>
{% endblock %}
<!--book_detail.html-->
{% extends 'base.html' %}
{% block content %}
    <p>书名:{{ book.1 }}</p>
    <p>作者:{{ book.2 }}</p>
    <form action="{% url 'delete_book' %}" method="post">
        <input type="hidden" name="book_id" value="{{ book.0 }}">
        <input type="submit" value="删除图书">
    </form>

{% endblock %}
```

css样式代码如下：

```css
*{
    margin:0;
    padding:0;
}

.nav{
    background: #3a3a3a;
    height: 65px;
    overflow:hidden;
}
.nav li{
    float:left;
    list-style: none;
    margin:0px 20px;
    line-height: 65px;
}
.nav li a{
    color: white;
    text-decoration: none;
}
.nav li a:hover{
    color:blue;
}
.index_table td{
    border:1px red dashed;
}
```

###### 注意事项

1、POST提交的时候报错 CSRF

解决：可以从setting.py的MIDDLEWARE中将`django.middleware.csrf.CsrfViewMiddleware`这行代码注释掉。

2、mysql数据库的连接

解决：在setting.py中找到`DATABASES`配置代码如下：

```python
DATABASES = {
    'default': {
        #'ENGINE': 'django.db.backends.sqlite3',
        'ENGINE': 'django.db.backends.mysql',#表示mysql引擎
        #'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
        'NAME': 'book_manager',#数据库名称
        'USER':'root',#数据库用户名
        'PASSWORD':'123456',#数据库密码
        'HOST':'127.0.0.1',#数据库ip地址
        'PORT':'3306',#数据库端口
    }
}
```



