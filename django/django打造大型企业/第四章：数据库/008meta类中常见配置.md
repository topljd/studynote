## Meta类中常见配置

对于一些模型级别的配置，我们可以在模型中自定义一个类，叫做Meta。然后在这个类中添加一些类属性来控制模型的作用。比如我们想要在数据库映射的时候使用自己指定的表名，而不是使用模型的名称。那么我们可以在meta类中添加一个`db_table`的属性，示例代码如下：

```python
class Author(models.Model):
    username = models.CharField(max_length=100,null=True)#默认不能为空
    age = models.IntegerField(null = True)
    #db_column='author_age'加入这个参数，就会将数据表中的age改变为author_age
    #在类里面进行定义Meta类 Rename table for author to author
    class Meta:
        db_table = 'author'
```

#### db_table

这个模型映射到数据库中的表明，如果没有指定这个参数，那么在映射的时候会将使用模型名来作为默认的表名。

#### ordering

设置在提取数据的排序方式，后面章节会讲到如何查找数据，比如我想在查找数据的时候根据添加的时间排序，那么代码如下：

```python
class Author(models.Model):
    username = models.CharField(max_length=100,null=True)#默认不能为空
    age = models.IntegerField(null = True)
    #db_column='author_age'加入这个参数，就会将数据表中的age改变为author_age
    #在类里面进行定义Meta类 Rename table for author to author
    def __str__(self):
        return "<(Author id:%s ,create_tiem:%s)>" %(self.id,self.create_time)
    class Meta:
        db_table = 'author'
        ordering = ['pub_date','id']#可以多个参数
       #如果变为 -pub_date,前面加负号就表示反向了
       #先按照第一个参数排序在按照后面的参数
```
```python
def order_view(request):
	authors = Author.objects.all()
    for author in authors:
        #<Author object>
        print(author)
    return HttpResponse('success')
```
> Meta类定义在数据表的类中：
>
> class Meta:
>
> ​	pass









