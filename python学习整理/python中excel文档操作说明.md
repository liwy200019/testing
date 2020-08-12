# python中excel文档操作库

## python中xlrd的用法：

### (1) 打开excel文件并获取所有sheet

```
>>> import xlrd
>>> workbook = xlrd.open_workbook(r'D:\Program Files\Notepad++\Student.xlsx')
>>> print workbook.sheet_names()
[u'Sheet1', u'Sheet2', u'Sheet3']
```

### (2) 根据下标获取sheet名称

```
>>> sheet2_name=workbook.sheet_names()[1]
>>> print sheet2_name
Sheet2
```

### (3) 根据sheet索引或者名称获取sheet内容，同时获取sheet名称、行数、列数

```
>>> sheet2 = workbook.sheet_by_index(1)
>>> print sheet2.name,sheet2.nrows,sheet2.ncols
Sheet2 6 5
>>> sheet2 = workbook.sheet_by_name('Sheet2')
>>> print sheet2.name,sheet2.nrows,sheet2.ncols
Sheet2 6 5
```

### (4) 根据sheet名称获取整行和整列的值

```
>>> sheet2 = workbook.sheet_by_name('Sheet2')
>>> rows = sheet2.row_values(3)
>>> cols = sheet2.col_values(2)
>>> print rows
[u'lisi2', 19.0, 41462.0, u'basketball', u'friend2'] #标红部分为日期2013/7/7，实际却显示为浮点数。后面有描述如何纠正
>>> print cols
[u'\u51fa\u751f\u65e5\u671f', 42129.0, 41796.0, 41462.0, 40941.0, u''] # 问题同上
>>>
```

### (5）获取指定单元格的内容



```
>>> print sheet2.cell(1,0).value.encode('utf-8')
xiaoming2
>>> print sheet2.cell_value(1,0).encode('utf-8')
xiaoming2
>>> print sheet2.row(1)[0].value.encode('utf-8')
xiaoming2
```



### (6）获取单元格内容的数据类型



```
>>> print sheet2.cell(1,0).ctype #第2行第1列:xiaoming2 为string类型
1
>>> print sheet2.cell(1,1).ctype #第2行第2列:12  为number类型
2
>>> print sheet2.cell(1,2).ctype #第2行第3列:2015/5/5 为date类型
3
```



说明：ctype : 0 empty,1 string, 2 number, **3 date**, 4 boolean, 5 error

### (7）获取单元内容为日期类型的方式

  使用xlrd的xldate_as_tuple处理为date格式，先判断表格的ctype=3时xlrd才能执行操作，如下：



```
>>> from datetime import datetime,date
>>> sheet2.cell(1,2).ctype
3
>>> sheet2.cell(1,2).value
42129.0
>>> xlrd.xldate_as_tuple(sheet2.cell_value(1,2),workbook.datemode)
(2015, 5, 5, 0, 0, 0)
>>> date_value = xlrd.xldate_as_tuple(sheet2.cell_value(1,2),workbook.datemode)
>>> date(*date_value[:3])
datetime.date(2015, 5, 5)
>>> date(*date_value[:3]).strftime('%Y/%m/%d')
'2015/05/05'
```



那么如果是在脚本中需要获取并显示单元格内容为日期类型的，可以先做一个判断。判断ctype是否等于3，如果等于3，则用时间格式处理：

```
if (sheet.cell(row,col).ctype == 3):
  date_value = xlrd.xldate_as_tuple(sheet.cell_value(row,col),book.datemode)
  date_tmp = date(*date_value[:3]).strftime('%Y/%m/%d')
```

### (8) 获取合并单元格的内容



```
>>> sheet2.cell(1,4).value #第4列的第2行和第3行是合并单元格
u'friend'
>>> sheet2.cell(2,4).value
u''
>>> sheet2.cell(5,1).value #第6行的第2和第3第4列是合并单元格，这里我们只获取到第6行第2列的值而第3列第4列获取的内容为空，如何处理?
u'None'
>>> sheet2.cell(5,2).value
u''
>>> sheet2.cell(5,3).value
u''
```

## Python中xlwt的用法

import xlwt
创建一个工作表对象
workbook = xlwt.Workbook(encoding=‘utf-8’)
设置excel表名
sheet = workbook.add_sheet(‘工作表’)
往表格中填充数据
第一个参数表示行号，第二个参数表示列号
sheet.write(0,0,‘姓名’)
sheet.write(0,1,‘年龄’)
sheet.write(0,2,‘身高’)
sheet.write(0,3,‘体重’)

sheet.write(1,0,‘李青’)
sheet.write(1,1,‘20’)
sheet.write(1,2,‘170’)
sheet.write(1,3,‘65KG’)
workbook.save(‘工作表.xls’)

## Python中xlutils解析

### 1、导入模块

import xlrd

import xlutils.copy

### 2、打开模块表

book = xlrd.open_workbook('test.xls', formatting_info=True)

### 3、复制模块表

wtbook = xlutils.copy.copy(book)

wtsheet = wtbook.get_sheet(0)

### 4、写入模块表

wtsheet.write(0, 0, 'Ok, changed!')

### 5、保存模块表

wtbook.save('test.xls')

\#调用xlrd.open_workbook()时，如果不指定formatting_info=True，那么修改后整个文档的样式会丢失。对一个单元格进行write操作时，如果不指定样式，也会将原来的样式丢失。

注意调用copy()的方法。也可以通过声明from xlutils.copy import copy来直接调用copy()。