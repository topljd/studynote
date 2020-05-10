### django使用原生sql语句操作数据库

#### django配置连接数据库

> 在setting.py 里面的`DATABASES`进行配置

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
        #这里的name表示引用的是db.sqlite3的文件，前面是路径

    }
}
```

#### 数据库的配置

```python
DATABASES = {
    'default': {
        #数据库引擎(sqlite3/mysql/orcale等)
        'ENGINE': 'django.db.backends.mysql',
        #数据库的名字
        'NAME': 'django_db1',
        #连接数据库的用户名
        'USER':'root',
        #连接数据库的密码
        'PASSWORD':'123456',
        #连接数据库的主机地址
        'HOST':'127.0.0.1',
        #连接数据库的端口
        'PORT':'3306'

    }
}
```

#### 默认模板

```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [os.path.join(BASE_DIR, 'templates')],
        #系统的位置
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```



#### 连接数据库

在django中使用原生sql语句操作，就是使用python db api的接口操作。如果mysql驱动使用的是pymysql那么就是使用pymysql操作。

##### 插入数据库数据

```python
def index(request):
    #连接游标
    cursor = connection.cursor()
    cursor.execute("insert into book(id , name ,author) values(null,'三国演义','罗贯中')")
    return render(request,'index.html')
#查找数据
	cursor = connection.cursor()
    #cursor.execute("insert into book(id , name ,author) values(null,'三国演义','罗贯中')")
    cursor.execute('select id,name,author from book')
    rows = cursor.fetchall()
    for row in rows:
        print(row)
```

####  Python DB api 规范

可以查看官方的文档

![1589120279264](H:\studynote\django\django打造大型企业\第四章：数据库\images\1589120279264.png)





