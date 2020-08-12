# python中，ddt模块的使用

## ddt（数据驱动）应用场景：

我们做接口自动化的时候，同一个业务场景下，会有多组数据的录入的情况，这个时候如果每录入一组数据就要写一个测试类的话，太过繁琐，也不符合测试思想。这个时候数据驱动思想（ddt模块）就有了用武之地。

### 安装

pip install ddt

### 基础：

#### 1.ddt获取到的数据为多个字典的list类型（列表里面的元素是字典类型）

#### 2.只要运行ddt框架，会自动调用list里面的测试数据，生成对应个数的用例。

#### 3.ddt需要与单元测试框架unittest一起使用

#### 用法：

ddt其实是一个装饰器，什么是装饰器？移步https://www.runoob.com/w3cnote/python-func-decorators.html

ddt.ddt装饰测试类

ddt.data（*data_case）装饰测试用例，*号意为解包。入参为测试数据，ddt会按逗号分隔，将数据拆分

如下：

```
data_case = ({'name': 'qwl', 'age': '18'}, {'name': '吴彦祖', 'age': '38'})
#此时用ddt.data就可以将数据按逗号解包为单条数据用例。后续可以通过键值对的方式将值取出
@ddt.ddt
class A(unittest.TestCase):    
@ddt.data(*data_case)    
	def def_a(self, data):
    pass
```

 

以上只是简单的使用了ddt模块进行数据驱动，实际项目应用过程中应该还需要更多知识的结合使用。

[https://blog.csdn.net/u013118036/article/details/45558951](https://links.jianshu.com/go?to=https%3A%2F%2Fblog.csdn.net%2Fu013118036%2Farticle%2Fdetails%2F45558951)

[http://www.cnblogs.com/nuonuozhou/p/8645129.html](https://links.jianshu.com/go?to=http%3A%2F%2Fwww.cnblogs.com%2Fnuonuozhou%2Fp%2F8645129.html)

这篇文章将ddt模块与xldr excel一起使用，进行数据驱动

