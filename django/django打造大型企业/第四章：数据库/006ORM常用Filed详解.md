#### 模型常用属性

##### 常用字段：

在Django中，定义了一些Field来与数据库表中的字段类型来进行映射。以下将介绍那些常用的字段类型。

------

######  AutoField：

映射到数据库中`int`类型，可以有自动增长的特性。一般不需要使用这个类型，如果不指定主键，那么模型会自动的生成一个叫`id`的自动增长的主键，如果你想指定一个其他名字的并且具有自动增长的主键，使用`AutoField`也是可以的。

###### BigAutoField

64位的整型，类似于AutoFiled，只不过是产生的数据的范围是从`1-9223372036854775807`

###### BooleanField

在模型层面接受的是`True/False`。在数据库层面是`tinyint`类型。如果没有指定默认值，默认值是None。

###### CharFiled

在数据库层面是`varchar`类型。在python层面就是普通的字符串，这个类型在使用的时候必须要指定最大的长度，也即必须要传递`max_length`这个关键字参数进去。

###### DateField

日期类型：在python中是`datatime.date`类型，可以记录年月日，在映射到数据库中也是`date`类型。使用这个`field`可以传递以下几个参数：

1、`auto_now`在每次这个数据保存的时候后，都使用当前的时间，比如作为一个记录修改日期的字段，可以将这个属性设置为`True`

2、`auto_now_add`在每次数据第一次添加进去的时候，都使用当前时间，比如作为一个记录第一次入库的字段，可以将这个属性设置为`True`。

###### DateTimeField：

日期时间类型，类似于`DataField`。不仅仅存储日期，还可以存储时间。映射到数据库中`datetime`类型，这个`filed`也可以使用`auto_now`和`auto_now_add`两个属性。

###### TimeField：

时间类型。在数据库中是`time`类型。在python中是`datetime.time`类型。

###### EmailField:

类似于`CharField`。在数据库底层也是一个`varchar`类型。最大长度是254字符。

```
EmailField在数据库层面并不会限制字符串一定要满足邮箱格式
只是以后再使用modelForm等表单相关操作的时候回起作用
```

###### FileField

用来存储文件的，这个参考后面的文件上传文章节部分。

###### ImageFiled

用来存储图片文件的，这个请参考后面的图片上传章节部分。

###### FloatField

浮点类型。映射到数据库中是`float`类型。

###### IntegerField

整型。值得区间是`-2147483648——2147483647`.

###### BigInterField

大整型。值的区间是`-9223372036854775808——9223372036854775807`.

###### PositiveIntegerField

正整型。值得区间是`0-2147483647`

###### SmallIntegerField:

小整型，值得区间是`-32768-32767`

###### PositiveSmallIntegerField:

正小整型。值得区间是`0-32767`

###### TextField:

大量的文本类型。映射到数据库中是longtext类型。

###### UUIDField

只能存储uuid格式的字符串，uuid是一个32位的全球唯一的字符串，一般用来作为主键。

###### URLField

类似于`CharField`，只不过能用来存储`url`格式的字符串，并且默认的`max_length`是200.

----

如果想查看更多的字段，选择某个字段`Ctrl+左单击`就可以看到！

#### Field的常用参数：

#####  null:				null = True

如果设置为`True`，`Django`将会在映射表的时候指定是否为空。默认是为`Flase`。在使用字符串相关的Field（CharField/TextField)的时候，官方推荐尽量不要使用这个参数，也就是保持默认值`Flase`.因为`Django`在处理字符串相关的Field的时候，及时这个Field的null=False，如果你没有给这个Field传递任何值，那么`Django`也会使用一个空的字符串''来作为默认值存储进去，因此如果再使用null=True,Django会产生两种空值得情形（NULL或者空字符串）。如果想要在表单验证的时候允许这个字符串为空，那么建议使用`blank=True`。如果你的`Field`是BooleanField，那么对应的可空的字段则为`NullBooleanField`。

```python
#models.py
class Author(models.Model):
    username = models.CharField(max_length=100)#默认不能为空
    age = models.IntegerField(null = True)
#views.py
def null_text_field(request):
    author = Author(username='')
    author.save()
    return HttpResponse('null')
```

![image-20200408180431209](C:\Users\82023\AppData\Roaming\Typora\typora-user-images\image-20200408180431209.png)

#### blank

标识这个字段在表单验证的时候是否可以为空，默认是False。

这个和`null`是有区别的，`null`是一个纯数据库级别的，而`blank`是表单验证级别的。

#### db_column:

这个歌字段在数据库中的名字，如果没有设置这个参数，那么将会使用模型中属性的名字。

```python
age = models.IntegerField(null = True,db_column='author_age')
#db_column='author_age'加入这个参数，就会将数据表中的age改变为author_age
```

#### default：

默认值，可以为一个值，或者是一个函数，但是不支持`lambda`表达式，并且不支持列表/字典/集合等可变得数据结构。

```python
age = models.IntegerField(null = True,db_column='author_age',default =0)
#default =0,字段默认为0 如果不给值得时候就会默认为0
now()
调用default = now  后面不需要括号，这样才是调用函数
```

#### auto_now_add

auto_now_add= ture 自动加入创建的时间

#### primary_key

是否为主键，默认是False。

#### unique

在表中这个字段的值是否唯一。一般是设置手机号码/邮箱等。

```python
telephone = models.CharField(max_length=11,unique =True,null=True)
```

解决之前存在数据唯一的问题，添加`null = True`

-----

其他字段可以查看官方文档：

https://docs.djangoproject.com/zh-hans/2.0/ref/models/fields



