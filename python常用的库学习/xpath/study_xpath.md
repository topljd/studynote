### XPath语法

> XPath使用路径表达式来选取XML文档中的节点或节点集。节点是通过沿着路径或者步来选取的。

#### XML实例文档

```auto
<?xml version="1.0" encoding="ISO-8859-1"?>

<bookstore>

<book>
  <title lang="eng">Harry Potter</title>
  <price>29.99</price>
</book>

<book>
  <title lang="eng">Learning XML</title>
  <price>39.95</price>
</book>

</bookstore>
```

下面列出最有用的路径表达式：

| 表达式   | 描述                                                     |
| -------- | -------------------------------------------------------- |
| nodename | 选取此节点的所有子节点                                   |
| /        | 从根节点选取                                             |
| //       | 从匹配选择的当前节点选择文档中的节点，而不考虑他们的位置 |
| .        | 选取当前节点                                             |
| ..       | 选取当前节点的父节点                                     |
| @        | 选取属性                                                 |

#### 实例：

| 路径表达式      | 结果                                                         |
| --------------- | ------------------------------------------------------------ |
| bookstore       | 选取 bookstore 元素的所有子节点。                            |
| /bookstore      | 选取根元素 bookstore。注释：假如路径起始于正斜杠( / )，则此路径始终代表到某元素的绝对路径！ |
| bookstore/book  | 选取属于 bookstore 的子元素的所有 book 元素。                |
| //book          | 选取所有 book 子元素，而不管它们在文档中的位置。             |
| bookstore//book | 选择属于 bookstore 元素的后代的所有 book 元素，而不管它们位于 bookstore 之下的什么位置。 |
| //@lang         | 选取名为 lang 的所有属性。                                   |

二、谓词：被嵌在方括号内，用来查找某个特定的节点或包含某个制定的值的节点

| 表达式                            | 结果                                 |
| --------------------------------- | ------------------------------------ |
| xpath(‘/body/div[1]’)             | 选取body下的第一个div节点            |
| xpath(‘/body/div[last()]’)        | 选取body下最后一个div节点            |
| xpath(‘/body/div[last()-1]’)      | 选取body下倒数第二个div节点          |
| xpath(‘/body/div[positon()<3]’)   | 选取body下前两个div节点              |
| xpath(‘/body/div[@class]’)        | 选取body下带有class属性的div节点     |
| xpath(‘/body/div[@class=”main”]’) | 选取body下class属性为main的div节点   |
| xpath(‘/body/div[price>35.00]’)   | 选取body下price元素值大于35的div节点 |

三、通配符：Xpath通过通配符来选取未知的XML元素

| 表达式            | 结果                    |
| ----------------- | ----------------------- |
| xpath（’/div/*’） | 选取div下的所有子节点   |
| xpath(‘/div[@*]’) | 选取所有带属性的div节点 |


四、取多个路径：使用“ | 运算符可以选取多个路径

| 表达式    | 结果   |
| -------------- | ------ |
| xpath(‘//div\|//table’) | 选取所有的div和table节点|
五、Xpath轴：轴可以定义相对于当前节点的节点集 

| 轴名称           | 表达式                         | 描述                                         |
| ---------------- | ------------------------------ | -------------------------------------------- |
| ancestor         | xpath(‘./ancestor::*’)         | 选取当前节点的所有先辈节点（父、祖父）       |
| ancestor-or-self | xpath(‘./ancestor-or-self::*’) | 选取当前节点的所有先辈节点以及节点本身       |
| attribute        | xpath(‘./attribute::*’)        | 选取当前节点的所有属性                       |
| child            | xpath(‘./child::*’)            | 返回当前节点的所有子节点                     |
| descendant       | xpath(‘./descendant::*’)       | 返回当前节点的所有后代节点（子节点、孙节点） |
| following        | xpath(‘./following::*’)        | 选取文档中当前节点结束标签后的所有节点       |
| following-sibing | xpath(‘./following-sibing::*’) | 选取当前节点之后的兄弟节点                   |
| parent           | xpath(‘./parent::*’)           | 选取当前节点的父节点                         |
| preceding        | xpath(‘./preceding::*’)        | 选取文档中当前节点开始标签前的所有节点       |

| preceding-sibling | xpath(‘./preceding-sibling::*’) | 选取当前节点之前的兄弟节点 |
| ----------------- | ------------------------------- | -------------------------- |
| self              | xpath(‘./self::*’)              | 选取当前节点               |


六、功能函数：使用功能函数能够更好的进行模糊搜索

| 函数        | 用法                                                      | 解释                        |
| ----------- | --------------------------------------------------------- | --------------------------- |
| starts-with | xpath(‘//div[starts-with(@id,”ma”)]‘)                     | 选取id值以ma开头的div节点   |
| contains    | xpath(‘//div[contains(@id,”ma”)]‘)                        | 选取id值包含ma的div节点     |
| and         | xpath(‘//div[contains(@id,”ma”) and contains(@id,”in”)]‘) | 选取id值包含ma和in的div节点 |
| text()      | xpath(‘//div[contains(text(),”ma”)]‘)                     | 选取节点文本包含ma的div节点 |

七、常用函数：
 1、精确定位
（1）contains(str1,str2)用来判断str1是否包含str2
例1：//*[contains(@class,'c-summaryc-row ')]选择@class值中包含c-summary c-row的节点
例2：//div[contains(.//text(),'价格')]选择text()中包含价格的div节点
（2）position()选择当前的第几个节点
例1：//\*\[@class='result'][position()=1]选择@class='result'的第一个节点
例2：//\*\[@class='result'][position()<=2]选择@class='result'的前两个节点
（3）last()选择当前的倒数第几个节点
例1：//\*\[@class='result'][last()]选择@class='result'的最后一个节点
例2：//\*\[@class='result'][last()-1]选择@class='result'的倒数第二个节点

（4）following-sibling 选取当前节点之后的所有同级节点
例1：//div[@class='result']/following-sibling::div选择@class='result'的div节点后所有同级div节点找到多个节点时可通过position确定第几个如：//div[@class='result']/following-sibling::div[position()=1]

（5）preceding-sibling 选取当前节点之前的所有同级节点
使用方法同following-sibling
2、过滤信息
（1）substring-before(str1,str2)用于返回字符串str1中位于第一个str2之前的部分
例子：substring-before(.//*[@class='c-more_link']/text(),'条')
返回.//*[@class='c-more_link']/text()中第一个'条'前面的部分，如果不存在'条'，则返回空值
（2）substring-after(str1,str2)跟substring-before类似，返回字符串str1中位于第一个str2之后的部分
例1：substring-after(.//*[@class='c-more_link']/text(),'条')
返回.//*[@class='c-more_link']/text()中第一个’条’后面的部分，如果不存在'条'，则返回空值
例2：substring-after(substring-before(.//*[@class='c-more_link']/text(),'新闻'),'条')
返回.//*[@class='c-more_link']/text()中第一个'新闻'前面与第一个'条'后面之间的部分
（3）normalize-space()
用来将一个字符串的头部和尾部的空白字符删除，如果字符串中间含有多个连续的空白字符，将用一个空格来代替
例子：normalize-space(.//*[contains(@class,'c-summaryc-row ')])
（4）translate(string,str1,str2)
假如string中的字符在str1中有出现，那么替换为str1对应str2的同一位置的字符，假如str2这个位置取不到字符则删除string的该字符
例子：translate('12:30','03','54')结果：'12:45'

3、拼接信息
（1）concat()函数用于串连多个字符串
例子：concat('http://baidu.com',.//*[@class='c-more_link']/@href)