## python处理excel

####  python处理的优点

处理数据量大，方便快捷！

编程语言都是需要花一点时间去学习的！

### 读写Excel

#### 读取Excel中的数据

如果我们只是要`读取`Excel文件里的数据进行处理。可以使用`xlrd`这个库。

首先我们安装xlrd库，执行下面的命令：

```python
pip install xlrd
```

[点击这里下载文档](http://cdn1.python3.vip/files/py/income.xlsx)

这个文件里面有 3 个表单，分别记录了2018、2017、2016年 的月收入，如下所示。

> 如果我们想用程序统计 2016、2017、2018 三年所有月收入的总和，但是不要包含 `打星号` 的那些月份。怎么做？

xlrd 库里面的 open_workbook 函数打开Excel文件，并且返回一个 `Book对象` ，这个对象代表打开的 Excel 文件。

可以通过这个Book对象得到Excel文件的很多信息，比如 获取 Excel 文件中表单(sheet) 的数量 和 所有 表单(sheet) 的名字。

```python
import xlrd
#导入xlrd模块

#打开Excel文件
book = xlrd.open_workbook('income.xlsx')
print(f"包含表单数量 {book.nsheets}")# 3
print(f"表单的名分别为: {book.sheet_names()}")
#['2018', '2017', '2016']
```

要读取某个表单里单元格中的数据，必须要先获取 `表单（sheet）对象` 。

可以根据表单的索引 或者 表单名获取 表单对象，使用如下对应的方法

```python
import xlrd
book = xlrd.open_workbook('G:/xls/income.xlsx')
print(book.nsheets)
print(book.sheet_names())

# 表单索引从0开始，获取第一个表单对象
ws = book.sheet_by_index(0)
print(ws)
#<xlrd.sheet.Sheet object at 0x0000028AC5AC80D0>

# 获取名为2018的表单对象
ws1 = book.sheet_by_name('2018')
print(ws1)
#<xlrd.sheet.Sheet object at 0x00000168C32790D0>

# 获取所有的表单对象，放入一个列表返回
ws_list = book.sheets()
print(ws_list)
#[<xlrd.sheet.Sheet object at 0x0000016FCD5380A0>, <xlrd.sheet.Sheet object at 0x0000016FCD5380D0>, <xlrd.sheet.Sheet object at 0x0000016FCD538100>]
```

表单对象所有属性和方法的描述， 可以[点击这里，查看官方文档](https://xlrd.readthedocs.io/en/latest/api.html#xlrd-sheet)

获取了表单对象后，可以根据其属性得到：

```auto
表单行数（nrows）
列数（ncols）
表单名（name）
表单索引（number）
```

如下：

````python
import xlrd
book = xlrd.open_workbook('G:/xls/income.xlsx')

sheet = book.sheet_by_index(0)
print(f"表单名：{sheet.name} ")
print(f"表单索引：{sheet.number}")
print(f"表单行数：{sheet.nrows}")
print(f"表单列数：{sheet.ncols}")
'''
表单名：2018 
表单索引：0
表单行数：13
表单列数：2
'''
````

获取了表单对象后，可以使用 `cell_value` 方法，参数为行号和列号，读取指定单元格中的文本内容。如下所示：

```python
import xlrd
book = xlrd.open_workbook('G:/xls/income.xlsx')

sheet = book.sheet_by_index(0)

# 行号、列号都是从0开始计算
print(f"单元格A1内容是: {sheet.cell_value(rowx=0, colx=0)}")
#单元格A1内容是: 月份
```

还可以使用 `row_values` 方法，参数为行号，读取指定行所有单元格的内容，存放在一个列表中返回。

```python
import xlrd

book = xlrd.open_workbook("income.xlsx")

sheet = book.sheet_by_index(0)

# 行号、列号都是从0开始计算
print(f"第一行内容是: {sheet.row_values(rowx=0)}")

#运行结果：第一行内容是: ['月份', '收入']
```

还可以使用 `col_values` 方法，参数为列号，读取指定列所有单元格的内容，存放在一个列表中返回。

如下所示：

```python
import xlrd

book = xlrd.open_workbook("income.xlsx")

sheet = book.sheet_by_index(0)

# 行号、列号都是从0开始计算
print(f"第一列内容是: {sheet.col_values(colx=0)}")
```

运行输出结果：

```
第一列内容是: ['月份', 1.0, 2.0, 3.0, 4.0, 5.0, 6.0, 7.0, 8.0, 9.0, 10.0, 11.0, 12.0]
```

可以看出，数字以小数的形式返回了。

有了这些方法，我们就可以完成一些数据处理任务了。比如我们要计算 2017年 全年的收入就可以这样。

```python
import xlrd

book = xlrd.open_workbook("income.xlsx")

sheet = book.sheet_by_name('2017')

# 收入在第2列(第二列开始的行为1)
incomes = sheet.col_values(colx=1,start_rowx=1)
print(incomes)
#[20103.0, 20773.0, 20773.0, 20773.0, 20773.0, 20774.0, 20775.0, 20776.0, 20777.0, 20778.0, 20779.0, 20780.0]

print(f"2017年收入为: {sum(incomes)}")
```

那么我们怎么在汇总收入中，去掉包含星号的月份收入呢？

就需要我们查出哪些月份是带星号的，不要统计在内.

```python
sheet = book.sheet_by_name('2017')

# 收入在第2列
incomes = sheet.col_values(colx=1,start_rowx=1)

print(f"2017年账面收入为: {int(sum(incomes))}")

# 去掉包含星号的月份收入
toSubstract = 0
# 月份在第1列
monthes = sheet.col_values(colx=0)

for row,month in enumerate(monthes):
    if type(month) is str and month.endswith('*'):
        income = sheet.cell_value(row,1)
        print(month,income)
        toSubstract += income

print(f"2017年真实收入为: {int(sum(incomes)- toSubstract)}")
```

> 2017年账面收入为: 248634
> 6* 20774.0
> 2017年真实收入为: 227860

最后，要得到3年的收入，就要获取所有的sheet对象，采用上面的计算方法，最后把收入相加。

```python
import xlrd

book = xlrd.open_workbook("income.xlsx")

# 得到所有sheet对象
sheets = book.sheets()

incomeOf3years = 0
for sheet in sheets:
    # 收入在第2列
    incomes = sheet.col_values(colx=1,start_rowx=1)
    # 去掉包含星号的月份收入
    toSubstract = 0
    # 月份在第1列
    monthes = sheet.col_values(colx=0)

    for row,month in enumerate(monthes):
        if type(month) is str and month.endswith('*'):
            income = sheet.cell_value(row,1)
            print(month,income)
            toSubstract += income

    actualIncome = int(sum(incomes)- toSubstract)
    print(f"{sheet.name}年真实收入为: {actualIncome}")
    incomeOf3years += actualIncome

print(f'全部收入为{incomeOf3years}')
```

> 3* 30105.0
> 7* 30109.0
> 2018年真实收入为: 301088
> 6* 20774.0
> 2017年真实收入为: 227860
> 9* 19374.0
> 2016年真实收入为: 213084
> 全部收入为742032

### 新建Excel,写入数据

`xlrd` 只能读取Excel内容，如果你要 `创建` 一个新的Excel并 `写入` 数据，可以使用 `openpyxl` 库。

`openpyxl` 库既可以读文件、也可以写文件、也可以修改文件。

但是，openpyxl 库不支持老版本 Office2003 的 `xls` 格式的Excel文档，如果要读写xls格式的文档，可以使用 Excel 进行相应的格式转化。

执行`pip install openpyxl`安装该库

下面的代码，演示了openpyxl的一些基本用法。

```python
import openpyxl

# 创建一个Excel workbook 对象
book = openpyxl.Workbook()

# 创建时，会自动产生一个sheet，通过active获取
sh = book.active

# 修改当前 sheet 标题为 工资表
sh.title = '工资表'

# 保存文件
book.save('信息.xlsx')

# 增加一个名为 '年龄表' 的sheet，放在最后
sh1 = book.create_sheet('年龄表-最后')

# 增加一个 sheet，放在最前
sh2 = book.create_sheet('年龄表-最前',0)

# 增加一个 sheet，指定为第2个表单
sh3 = book.create_sheet('年龄表2',1)

# 根据名称获取某个sheet对象
sh = book['工资表']

# 给第一个单元格写入内容
sh['A1'] = '你好'

# 获取某个单元格内容
print(sh['A1'].value)

# 根据行号列号， 给第一个单元格写入内容，
# 注意和 xlrd 不同，是从 1 开始
sh.cell(2,2).value = '白月黑羽'

# 根据行号列号， 获取某个单元格内容
print(sh.cell(1, 1).value)

book.save('信息.xlsx')
```

下面的示例代码将保存在字典中的年龄表的内容写入到excel文件中

```python
import openpyxl

name2Age = {
    '张飞' :  38,
    '赵云' :  27,
    '许褚' :  36,
    '典韦' :  38,
    '关羽' :  39,
    '黄忠' :  49,
    '徐晃' :  43,
    '马超' :  23,
}

# 创建一个Excel workbook 对象
book = openpyxl.Workbook()

# 创建时，会自动产生一个sheet，通过active获取
sh = book.active

sh.title = '年龄表'

# 写标题栏
sh['A1'] =  '姓名'
sh['B1'] =  '年龄'

# 写入内容
row = 2

for name,age in name2Age.items():
    sh.cell(row, 1).value = name
    sh.cell(row, 2).value = age
    row += 1
#name2Age.items()这个获取字典的 键和值
# 保存文件
book.save('信息.xlsx')
```

#### 修改Excel中的数据

如果你想 `修改` 已经存在的Excel 文件，也可以使用 `openpyxl` 库。

#### 修改单元格内容

比如：

```python
import openpyxl

# 加载 excel 文件
wb = openpyxl.load_workbook('income.xlsx')

# 得到sheet对象
sheet = wb['2017']

sheet['A1'] = '修改一下'

## 指定不同的文件名，可以另存为别的文件
wb.save('income-1.xlsx')
```

#### 插入行、插入列

sheet 对象的 `insert_rows` 和 `insert_cols` 方法，分别用来插入 `行` 和 `列`， 比如

```python
import openpyxl

wb = openpyxl.load_workbook('income.xlsx')
sheet = wb['2018']

# 在第2行的位置插入1行(行是从1开始数的)
sheet.insert_rows(2)

# 在第3行的位置插入3行
sheet.insert_rows(3,3)

# 在第2列的位置插入1列
sheet.insert_cols(2)

# 在第2列的位置插入3列
sheet.insert_cols(2,3)

## 指定不同的文件名，可以另存为别的文件
wb.save('income-1.xlsx')
```

#### 删除行、删除列

sheet 对象的 `delete_rows` 和 `delete_cols` 方法，分别用来插入 `行` 和 `列`， 比如

```python
import openpyxl

wb = openpyxl.load_workbook('income.xlsx')
sheet = wb['2018']

# 在第2行的位置删除1行
sheet.delete_rows(2)

# 在第3行的位置删除3行
sheet.delete_rows(3,3)

# 在第2列的位置删除1列
sheet.delete_cols(2)

# 在第3列的位置删除3列
sheet.delete_cols(3,3)

## 指定不同的文件名，可以另存为别的文件
wb.save('income-1.xlsx')
```

### 文字 颜色、字体、大小

单元格里面的 `样式风格` （包括 颜色、字体、大小、下划线 等） 都是通过 `Font` 对象设定的

```python
import openpyxl
# 导入Font对象 和 colors 颜色常量
from openpyxl.styles import Font,colors

wb = openpyxl.load_workbook('income.xlsx')
sheet = wb['2018']

# 指定单元格字体颜色，
sheet['A1'].font = Font(color=colors.RED, #使用预置的颜色常量
                        size=15,    # 设定文字大小
                        bold=True,  # 设定为粗体
                        italic=True # 设定为斜体
                        )

# 也可以使用RGB数字表示的颜色
sheet['B1'].font = Font(color="981818")

# 指定整行 字体风格， 这里指定的是第3行
font = Font(color="981818")
for y in range(1, 100): # 第 1 到 100 列
    sheet.cell(row=3, column=y).font = font

# 指定整列 字体风格， 这里指定的是第2列
font = Font(bold=True)
for x in range(1, 100): # 第 1 到 100 行
    sheet.cell(row=x, column=2).font = font

wb.save('income-1.xlsx')
```

### 背景色

```python
import openpyxl
# 导入Font对象 和 colors 颜色常量
from openpyxl.styles import PatternFill

wb = openpyxl.load_workbook('income.xlsx')
sheet = wb['2018']

# 指定 某个单元格背景色
sheet['A1'].fill = PatternFill("solid", "E39191")

# 指定 整行 背景色， 这里指定的是第2行
fill = PatternFill("solid", "E39191")
for y in range(1, 100): # 第 1 到 100 列
    sheet.cell(row=2, column=y).fill = fill

wb.save('income-1.xlsx')
```

### 插入图片

下面是插入图片的代码

```python
import openpyxl
from openpyxl.drawing.image import Image

wb = openpyxl.load_workbook('income.xlsx')
sheet = wb['2018']

# 在第1行，第4列 的位置插入图片
sheet.add_image(Image('1.png'), 'D1')

## 指定不同的文件名，可以另存为别的文件
wb.save('income-1.xlsx')
```

