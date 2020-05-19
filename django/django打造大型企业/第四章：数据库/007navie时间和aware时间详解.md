## navie时间和aware时间

### 什么是naive时间，什么是aware时间

1、不知道自己的时间是表示的是哪个时区的，也就是不知道自己几斤几两，比较幼稚。

2、aware时间：知道自己的时间表示的是哪个时区的，也就是比较清醒。

### pytz库：

专门用来处理时区的库，这个库会经常更新一些时区的数据，不需要我们担心。并且这个库在安装django的时候回默认的安装，如果没有安装，那么可以通过`pip install pytz`的方式进行安装。

### astimezone方法：

将一个时区的时间转换为另外一个时区的时间。这个方法只能被`aware`类型的时间调用，不能被`naive`类型的时间调用。

```python
import pytz
from datetime import datetime
now = datetime.now() #这是一个naive类型的时间
utc_timezone = pytz.timezone("UTC")#定义UTC的时区对象
utc_now = now.astimezone(utc_timezone)#将当前的时间转换为UTC时区的时间
#>>ValueError:astimezone()cannot be applied to a naive datetime
#会抛出一个异常，原因就是因为navie类型的时间不能调用astimezone方法
now = now.replace(tzinfo = pytz.timezone('Asia/Shanhai'))
utc_now = now.astimezone(utc_timezone)
#这时候就可以正确的转换
```

>  USE_TZ = True
> #如果 USE_TZ 设置为false，那么django获取到当前时间就是一个naive类型的时间
> #建议设置为True

![1589120544893](images\1589120544893.png)

```python
from django.shortcuts import render
from django.http import HttpResponse
from .models import Article
from django.utils.timezone import now
# Create your views here.
def index(request):
    import pytz
    from datetime import datetime
    now = datetime.now()
    utc_timezone = pytz.timezone("UTC")
    utc_now = now.astimezone(utc_timezone)
    print(utc_now)#2020-04-06 10:20:42.961093+00:00
    return HttpResponse('success')
```

### replace方法：

可以将一个时间的某些属性进行更改

#### Django.utils.timezone.now方法：

会根据`settings.py`中是否设置了`USE_TZ=True`获取当前的时间，如果设置了，那么就获取一个`aware`类型的`UTC`时间。如果没有设置，那么会获取一个`navie`类型的时间。

#### django.utils.timezone.localtime方法：

#### navie和aware介绍以及在django中的用法

https://docs.djangoproject.com/en/2.0/topics/i18n/timezones/

> 时间的用法

**models**

```python
from django.db import models

# Create your models here.
class Article(models.Model):
    #付过想要使用自己定义的Field来作为主键，那么必须设置primary_key=True
    id = models.BigAutoField(primary_key = True)

    #BooleanField 布尔类型
    #如果没有指定null=True，那么默认情况下，null=False
    #就是不能为空null
    #如果想要使用可以为null的BooleanField，那么使用NullBooleanField来代替
    #removed = models.BooleanField()
    removed = models.NullBooleanField()#改过字段要重新映射
    #charField如果是超过了254个字符就不建议使用了
    #就推荐使用TextField：longtext
    title = models.CharField(max_length=200)
    create_time = models.DateTimeField(auto_now_add=True)
    #自动获取当前对象auto_now_add=True
    #创建时间 auto_now_add=True 是在第一次添加数据进去的时候会自动获取当前的时间
    #更新时间 auto_now =True:每次这个对象条用save方法的时候都会将当前的方法更新
#python manage.py makemigrations
#migrate
```

**views**

```python
from django.shortcuts import render
from django.http import HttpResponse
from .models import Article
from django.utils.timezone import now,localtime
#localtime可以根据timezone时间转换
# Create your views here.
def index(request):
    import pytz
    from datetime import datetime
    now = datetime.now()
    utc_timezone = pytz.timezone("UTC")
    utc_now = now.astimezone(utc_timezone)
    print(utc_now)#2020-04-06 10:20:42.961093+00:00

    #插入了两条数据
    article = Article(title='abc',create_time=now())
    article.save()
    #
    article = Article.objects.get(pk=7)
    # 如果数据库中没有id =7 的数据就会报错
    create_time = article.create_time
    print('='*30)
    print(create_time)
    print(localtime(create_time))
    print('=' * 30)
    # '''
    # ==============================
    # 2020-04-08 08:37:06.122458+00:00
    # [08/Apr/2020 16:45:04] "GET / HTTP/1.1" 200 7
    # 2020-04-08 16:37:06.122458+08:00
    # ==============================
    # '''
    return HttpResponse('success')
    context = {
        'create_time':create_time,
    }
    return  render(request,'index.html',context=context)
#=======================================
    article = Article(title='bcd')
    article.save()
    return HttpResponse('success')
```

记得在添加model的时候要运行：

- python manage.py makemigrations

  `在运行这句话的会后，如果字段以前的数据没有设置，要先进行设置`

- python manage.py migrate