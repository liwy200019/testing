# Python测试框架之pytest详解

## 前言

Python测试框架之前一直用的是unittest+HTMLTestRunner，听到有人说pytest很好用，所以这段时间就看了看pytest文档，在这里做个记录。

官方文档介绍：

> Pytest is a framework that makes building simple and scalable tests easy. Tests are expressive and readable—no boilerplate code required. Get started in minutes with a small unit test or complex functional test for your application or library.

pytest是一个非常成熟的全功能的Python测试框架，主要有以下几个特点：

- 简单灵活，容易上手
- 支持参数化
- 能够支持简单的单元测试和复杂的功能测试，还可以用来做selenium/appnium等自动化测试、接口自动化测试（pytest+requests）
- pytest具有很多第三方插件，并且可以自定义扩展，比较好用的如pytest-selenium（集成selenium）、pytest-html（完美html测试报告生成）、pytest-rerunfailures（失败case重复执行）、pytest-xdist（多CPU分发）等
- 测试用例的skip和xfail处理
- 可以很好的和jenkins集成
- report框架----allure 也支持了pytest

## 1、pytest安装

### **1.1安装**

```python
pip install -U pytest
```

### **1.2验证安装**

```
pytest --version # 会展示当前已安装版本
```

![img](https://img-blog.csdnimg.cn/20190809132420754.png)

### **1.3pytest文档**

官方文档：[https://docs.pytest.org/en/latest/contents.html](https://links.jianshu.com/go?to=https%3A%2F%2Fdocs.pytest.org%2Fen%2Flatest%2Fcontents.html)

**在pytest框架中，有如下约束：**

> 所有的单测文件名都需要满足test_*.py格式或*_test.py格式。
> 在单测文件中，测试类以Test开头，并且不能带有 **init** 方法(注意：定义class时，需要以T开头，不然pytest是不会去运行该class的)
> 在单测类中，可以包含一个或多个test_开头的函数。
> 此时，在执行pytest命令时，会自动从当前目录及子目录中寻找符合上述约束的测试函数来执行。

### **1.4 Pytest运行方式**

```
 # file_name: test_abc.py 
 import pytest # 引入pytest包 
 def test_a(): # test开头的测试函数     
 	print("------->test_a")     
 	assert 1 # 断言成功 
 def test_b():     
 	print("------->test_b")
 	assert 0 # 断言失败 
 if __name__ == '__main__':
 	pytest.main("-s  test_abc.py") # 调用pytest的main函数执行测试
```

1.测试类主函数模式

```
  pytest.main("-s  test_abc.py")
```

2.命令行模式

```
  pytest 文件路径／测试文件名  例如：pytest ./test_abc.py
```

### **1.5 Pytest Exit Code 含义清单**

- Exit code 0 所有用例执行完毕，全部通过
- Exit code 1 所有用例执行完毕，存在Failed的测试用例
- Exit code 2 用户中断了测试的执行
- Exit code 3 测试执行过程发生了内部错误
- Exit code 4 pytest 命令行使用错误
- Exit code 5 未采集到可用测试用例文件

### **1.6 如何获取帮助信息**

查看 pytest 版本

```
pytest --version
```

显示可用的内置函数参数

```
pytest --fixtures
```

通过命令行查看帮助信息及配置文件选项

```
pytest --help
```

### **1.7 控制测试用例执行**

1.在第N个用例失败后，结束测试执行

```
pytest -x                    # 第01次失败，就停止测试
pytest --maxfail=2     # 出现2个失败就终止测试
```

2.指定测试模块

```
pytest test_mod.py
```

3.指定测试目录

```
pytest testing/
```

4.通过关键字表达式过滤执行

```
pytest -k "MyClass and not method"
```

这条命令会匹配文件名、类名、方法名匹配表达式的用例，这里这条命令会运行 TestMyClass.test_something， 不会执行 TestMyClass.test_method_simple

5.通过 node id 指定测试用例

nodeid由模块文件名、分隔符、类名、方法名、参数构成，举例如下：
运行模块中的指定用例

```
pytest test_mod.py::test_func
```

运行模块中的指定方法

```
ytest test_mod.py::TestClass::test_method
```

6.通过标记表达式执行

```
pytest -m slow
```

这条命令会执行被装饰器 @pytest.mark.slow 装饰的所有测试用例

7.通过包执行测试

```
pytest --pyargs pkg.testing
```

这条命令会自动导入包 pkg.testing，并使用该包所在的目录，执行下面的用例。

### **1.8 多进程运行cases**

当cases量很多时，运行时间也会变的很长，如果想缩短脚本运行的时长，就可以用多进程来运行。

安装pytest-xdist：

```
pip install -U pytest-xdist
```

运行模式：

```
pytest test_se.py -n NUM
```

其中NUM填写并发的进程数。

### **1.9 重试运行cases**

在做接口测试时，有事会遇到503或短时的网络波动，导致case运行失败，而这并非是我们期望的结果，此时可以就可以通过重试运行cases的方式来解决。

安装pytest-rerunfailures：

```
pip install -U pytest-rerunfailures
```

运行模式：

```
pytest test_se.py --reruns NUM
```

NUM填写重试的次数。

### **1.10 显示print内容**

在运行测试脚本时，为了调试或打印一些内容，我们会在代码中加一些print内容，但是在运行pytest时，这些内容不会显示出来。如果带上-s，就可以显示了。

运行模式：

```
pytest test_se.py -s
```

另外，pytest的多种运行模式是可以叠加执行的，比如说，你想同时运行4个进程，又想打印出print的内容。可以用：

```
pytest test_se.py -s -n 4
```

 

## 2.Pytest的setup和teardown函数

> ### 1.setup和teardown主要分为：模块级,类级，功能级，函数级。
>
> ### 2.存在于测试类内部
>
> 代码示例：

- #### 2.1函数级别setup()／teardown()

> 运行于测试方法的始末，即:运行一次测试函数会运行一次setup和teardown

```
import pytestclass 
Test_ABC:  # 函数级开始
	def setup(self):
		print("------->setup_method")  # 函数级结束
	def teardown(self):      
		print("------->teardown_method")  
	def test_a(self):      
		print("------->test_a")      
		assert 1  
	def test_b(self):      
		print("------->test_b")
if __name__ == '__main__':
	pytest.main("-s  test_abc.py")
执行结果：  test_abc.py
------->setup_method # 第一次 setup()  
------->test_a  .  
------->teardown_method # 第一次 teardown()  
------->setup_method # 第二次 setup()  
------->test_b  .          
------->teardown_method # 第二次 teardown()
```

- #### 2.2.类级别

> 运行于测试类的始末，即:在一个测试内只运行一次setup_class和teardown_class，不关心测试类内有多少个测试函数。
> 代码示例：

```
import pytestclass 
Test_ABC:   # 测试类级开始   
	def setup_class(self):       
		print("------->setup_class")   # 测试类级结束   
	def teardown_class(self):       
		print("------->teardown_class")   
	def test_a(self):       
		print("------->test_a")       
		assert 1   
	def test_b(self):       
		print("------->test_b")          
if __name__ == '__main__':              
	pytest.main("-s  test_abc.py")
执行结果：  test_abc.py   
------->setup_class # 第一次 setup_class() 
------->test_a  .  ------->test_b  F      
------->teardown_class # 第一次 teardown_class()
```

## 3.Pytest配置文件

pytest的配置文件通常放在测试目录下，名称为pytest.ini，命令行运行时会使用该配置文件中的配置.

```
#配置pytest命令行运行参数   [pytest]    addopts = -s ... # 空格分隔，可添加多个命令行参数 -所有参数均为插件包的参数配置测试搜索的路径    testpaths = ./scripts  # 当前目录下的scripts文件夹 -可自定义#配置测试搜索的文件名称    python_files = test*.py #当前目录下的scripts文件夹下，以test开头，以.py结尾的所有文件 -可自定义配置测试搜索的测试类名    python_classes = Test_*     #当前目录下的scripts文件夹下，以test开头，以.py结尾的所有文件中，以Test开头的类 -可自定义配置测试搜索的测试函数名      python_functions = test_*#当前目录下的scripts文件夹下，以test开头，以.py结尾的所有文件中，以Test开头的类内，以test_开头的方法 -可自定义 
```

## 4 Pytest常用插件

> 插件列表网址：[https://plugincompat.herokuapp.com](https://links.jianshu.com/go?to=https%3A%2F%2Fplugincompat.herokuapp.com)
> 包含很多插件包，大家可依据工作的需求选择使用。

### 4.1 前置条件：

#### 1.文件路径：

```
- Test_App- - test_abc.py- - pytest.ini
```

#### 2.pyetst.ini配置文件内容：

```
  [pytest]# 命令行参数 addopts = -s# 搜索文件名 python_files = test_*.py # 搜索的类名 python_classes = Test_* #搜索的函数名    python_functions = test_*
```

### 4.2 Pytest测试报告

pytest-HTML是一个插件，pytest用于生成测试结果的HTML报告。兼容Python 2.7,3.6

安装方式：pip install pytest-html

```python
pip install pytest-html
```

> 通过命令行方式，生成xml/html格式的测试报告，存储于用户指定路径。插件名称：pytest-html
>
> 使用方法： 命令行格式：pytest --html=用户路径/report.html

示例：

```
import pytestclass 
Test_ABC:    
	def setup_class(self):        
		print("------->setup_class") 
	def teardown_class(self):    
		print("------->teardown_class") 
	def test_a(self):   
		print("------->test_a")   
		assert 1   
	def test_b(self):     
		print("------->test_b")          
        assert 0 # 断言失败```
        
        
运行方式：
1.修改Test_App/pytest.ini文件，添加报告参数，即：addopts = -s --html=./report.html     # -s:输出程序运行信息    # --html=./report.html 在当前目录下生成report.html文件    ️
若要生成xml文件，可将--html=./report.html 改成 --html=./report.xml
2.命令行进入Test_App目录
3.执行命令： pytest
执行结果：    1.在当前目录会生成assets文件夹和report.html文件 
```

## 5.pytest的高阶用法(一)

前置条件：

1.文件路径：

```
Test_App    - - test_abc.py    - - pytest.ini
```

2.pyetst.ini配置文件内容：

```
 [pytest]  命令行参数 addopts = -s 搜索文件名 python_files = test*.py  搜索的类名 python_classes = Test*搜索的函数名 python_functions = test_*
```

### 5.1pytest之fixture

fixture修饰器来标记固定的工厂函数,在其他函数，模块，类或整个工程调用它时会被激活并优先执行,通常会被用于完成预置处理和重复操作。

> 方法：fixture(scope="function", params=None, autouse=False, ids=None, name=None)
> 常用参数:

```
 scope：被标记方法的作用域 
 function" (default)：作用于每个测试方法，每个test都运行一次
 "class"：作用于整个类，每个class的所有test只运行一次 
 "module"：作用于整个模块，每个module的所有test只运行一次 
 "session：作用于整个session(慎用)，每个session只运行一次 
 params：(list类型)提供参数数据，供调用标记方法的函数使用 
 autouse：是否自动运行,默认为False不运行，设置为True自动运行
```

### 5.2fixture第一个例子(通过参数引用)

示例：

```
class Test_ABC:    
	@pytest.fixture()    
	def before(self):     
		print("------->before") 
	def test_a(self,before): # ️ test_a方法传入了被fixture标识的函数，已变量的形式   
		print("------->test_a")      
		assert 1
if __name__ == '__main__':  
	pytest.main("-s  test_abc.py")
执行结果：    test_abc.py    
------->before # 发现before会优先于测试函数运行    
------->test_a         .
```

### 5.3.fixture第二个例子(通过函数引用)

示例：

```
import pytest
@pytest.fixture() # fixture标记的函数可以应用于测试类外部
def before():   
	print("------->before")
@pytest.mark.usefixtures("before")
class Test_ABC:
	def setup(self):     
		print("------->setup")   
	def test_a(self):   
		print("------->test_a")      
		assert 1
if __name__ == '__main__':       
	pytest.main("-s  test_abc.py")  
执行结果：      test_abc.py 
------->before # 发现before会优先于测试类运行  
------->setup  
------->test_a      .
```

### 5.4.fixture第三个例子(默认设置为运行)

示例：

```
 import pytest 
 @pytest.fixture(autouse=True) # 设置为默认运行 
 def before():     
 	print("------->before") 
 class Test_ABC:     
 	def setup(self):   
 		print("------->setup") 
 	def test_a(self):       
 		print("------->test_a")     
 		assert 1 
 if __name__ == '__main__':    
 	pytest.main("-s  test_abc.py")
 执行结果：    test_abc.py     
 ------->before # 发现before自动优先于测试类运行 
 ------->setup   
 ------->test_a        .
```

### 5.5.fixture第四个例子(设置作用域为function)

示例：

```
import pytest  
@pytest.fixture(scope='function',autouse=True) # 作用域设置为function，自动运行  
def before():      
	print("------->before")   
class Test_ABC:       
	def setup(self):      
		print("------->setup")    
	def test_a(self):        
		print("------->test_a")    
		assert 1  
	def test_b(self):   
		print("------->test_b")     
		assert 1  
if __name__ == '__main__':    
	pytest.main("-s  test_abc.py")
执行结果：    test_abc.py  
------->before # 运行第一次 
------->setup      
------->test_a     
.------->before # 运行第二次   
------->setup    
------->test_b        .
```

### 5.6.fixture第五个例子(设置作用域为class)

示例：

```
import pytest    
@pytest.fixture(scope='class',autouse=True) # 作用域设置为class，自动运行 
def before():     
	print("------->before")   
class Test_ABC:  
	def setup(self):     
		print("------->setup")   
	def test_a(self):        
		print("------->test_a")  
		assert 1      
	def test_b(self):  
		print("------->test_b")      
		assert 1   
if __name__ == '__main__':     
	pytest.main("-s  test_abc.py")
执行结果：    test_abc.py   
------->before # 发现只运行一次   
------->setup     
------->test_a        .     
------->setup      
------->test_b        .
```

### 5.7.fixture第六个例子(返回值)

示例一:

```
import pytest  
@pytest.fixture()  
def need_data():       
	return 2 # 返回数字2    
class Test_ABC:        
	def test_a(self,need_data):       
		print("------->test_a")        
		assert need_data != 3 # 拿到返回值做一次断言  
if __name__ == '__main__':   
	pytest.main("-s  test_abc.py")
执行结果：    test_abc.py    
------->test_a    .`` 
```

示例二:

```
import pytest
@pytest.fixture(params=[1, 2, 3])
def need_data(request): # 传入参数request 系统封装参数   
	return request.param # 取列表中单个值，默认的取值方式
class Test_ABC:    
	def test_a(self,need_data):  
		print("------->test_a")     
		assert need_data != 3 # 断言need_data不等于3 
if __name__ == '__main__':  
	pytest.main("-s  test_abc.py")  
执行结果：      # 可以发现结果运行了三次      
test_abc.py       1     
------->test_a      .      2    
------->test_a      .      3      
------->test_a      F
```

## 6.Pytest高阶用法(二)

前置条件：

1.文件路径：

```
- Test_App- - test_abc.py- - pytest.ini
```

2.pyetst.ini配置文件内容：

```
[pytest]命令行参数addopts = -s搜索文件名python_files = test_*.py 搜索的类名python_classes = Test_* 搜索的函数名python_functions = test_*
```

### 6.1.跳过测试函数

```
根据特定的条件，不执行标识的测试函数. 方法：     skipif(condition, reason=None) 参数：     condition：跳过的条件，必传参数     reason：标注原因，必传参数 使用方法：     @pytest.mark.skipif(condition, reason="xxx") 
```

示例：

```
import pytest
class Test_ABC:
	def setup_class(self):  
		print("------->setup_class") 
	def teardown_class(self):  
		print("------->teardown_class")  
	def test_a(self): 
		print("------->test_a")       
		assert 1    
    @pytest.mark.skipif(condition=2>1,reason = "跳过该函数") # 跳过测试函数test_b  
    def test_b(self):  
        print("------->test_b")      
        assert 0
执行结果： 
test_abc.py 
------->setup_class  
------->test_a #只执行了函数test_a   .   
------->teardown_class       s # 跳过函数```  
```

### 6.2 标记为预期失败函数

```
标记测试函数为失败函数 方法：     xfail(condition=None, reason=None, raises=None, run=True, strict=False) 
常用参数：     
condition：预期失败的条件，必传参数     
reason：失败的原因，必传参数 
使用方法：     @pytest.mark.xfail(condition, reason="xx")
```

示例：

```
import pytest
class Test_ABC:  
    def setup_class(self):    
        print("------->setup_class")  
        def teardown_class(self):    
		print("------->teardown_class")  
    def test_a(self):      
        print("------->test_a")  
        assert 1  
    @pytest.mark.xfail(2 > 1, reason="标注为预期失败") # 标记为预期失败函数test_b     
    def test_b(self):   
        print("------->test_b")      
        assert 0  
执行结果：       
test_abc.py   
------->setup_class   
------->test_a       .  
------->test_b      
------->teardown_class       x  # 失败标记
```

### 6.3 函数数据参数化

```
方便测试函数对测试属于的获取。 
方法：     parametrize(argnames, argvalues, indirect=False, ids=None, scope=None) 
常用参数：     
argnames：参数名     
argvalues：参数对应值，类型必须为list                
当参数为一个时格式：[value]                 
当参数个数大于一个时，格式为:[(param_value1,param_value2.....),(param_value1,param_value2.....)] 
使用方法:     
@pytest.mark.parametrize(argnames,argvalues)     ️ 参数值为N个，测试方法就会运行N次   
```

单个参数示例：

```
import pytest
class Test_ABC:  
    def setup_class(self):  
    	print("------->setup_class")   
    def teardown_class(self):   
    	print("------->teardown_class")
    @pytest.mark.parametrize("a",[3,6]) # a参数被赋予两个值，函数会运行两遍
    def test_a(self,a): # 参数必须和parametrize里面的参数一致    
    	print("test data:a=%d"%a)    
    	assert a%3 == 0    
执行结果:    
test_abc.py
------->setup_class    
test data:a=3 # 运行第一次取值a=3    .    
test data:a=6 # 运行第二次取值a=6    .     
------->teardown_class
```

多个参数示例：

```
import pytest
class Test_ABC: 
    def setup_class(self):    
    	print("------->setup_class") 
    def teardown_class(self):      
    	print("------->teardown_class")
    @pytest.mark.parametrize("a,b",[(1,2),(0,3)]) # 参数a,b均被赋予两个值，函数会运行两遍
    def test_a(self,a,b): # 参数必须和parametrize里面的参数一致   
        print("test data:a=%d,b=%d"%(a,b))   
        assert a+b == 3    
执行结果：    
test_abc.py     
------->setup_class    
test data:a=1,b=2 # 运行第一次取值 a=1,b=2    .  
test data:a=0,b=3 # 运行第二次取值 a=0,b=3    .    
------->teardown_class
```

函数返回值类型示例：

```
import pytest
def return_test_data():   
	return [(1,2),(0,3)]
class Test_ABC:    
    def setup_class(self):    
   	 print("------->setup_class") 
    def teardown_class(self):    
    	print("------->teardown_class")
    @pytest.mark.parametrize("a,b",return_test_data()) # 使用函数返回值的形式传入参数值
    def test_a(self,a,b):  
    	print("test data:a=%d,b=%d"%(a,b))  
    	assert a+b == 3      
执行结果：  
test_abc.py 
------->setup_class   
test data:a=1,b=2 # 运行第一次取值 a=1,b=2    .   
test data:a=0,b=3 # 运行第二次取值 a=0,b=3    .    
------->teardown_class
```

### 6.4 修改 Python traceback 输出

```
pytest --showlocals     # show local variables in tracebacks
pytest -l               # show local variables (shortcut)
pytest --tb=auto        # (default) 'long' tracebacks for the first and last                        # entry, but 'short' style for the other entries
pytest --tb=long        # exhaustive, informative traceback formatting
pytest --tb=short       # shorter traceback format
pytest --tb=line        # only one line per failure
pytest --tb=native      # Python standard library formatting
pytest --tb=no          # no traceback at all
```

**--full-trace** 参数会打印更多的错误输出信息，比参数 --tb=long 还多，即使是 Ctrl+C 触发的错误，也会打印出来

### 6.5 执行失败的时候跳转到 PDB

执行用例的时候，跟参数 --pdb，这样失败的时候，每次遇到失败，会自动跳转到 PDB

```
pytest --pdb              # 每次遇到失败都跳转到 PDBpytest -x --pdb           # 第一次遇到失败就跳转到 PDB，结束测试执行pytest --pdb --maxfail=3  # 只有前三次失败跳转到 PDB 
```

### 6.6 设置断点

在用例脚本中加入如下python代码，pytest会自动关闭执行输出的抓取，这里，其他test脚本不会受到影响，带断点的test上一个test正常输出

```
 import pdb; pdb.set_trace()
```

### 6.7 获取用例执行性能数据

获取最慢的10个用例的执行耗时

```
pytest --durations=10
```

### 6.8 生成 JUnitXML 格式的结果文件

这种格式的结果文件可以被Jenkins或其他CI工具解析

```
pytest --junitxml=path
```

### 6.9禁用插件 

例如，关闭 doctest 插件

```
pytest -p no:doctest
```

### 6.10 从Python代码中调用pytest

```
pytest.main()                      # 基本用法
pytest.main(['-x', 'mytestdir'])   # 传入配置参数  // 指定自定义的或额外的插件
# content of myinvoke.py
import pytest
class MyPlugin(object):   
	def pytest_sessionfinish(self):   
		print("*** test run reporting finishing")
pytest.main(["-qq"], plugins=[MyPlugin()])
```

### 6.11 测试脚本迁移后快速部署包含pytest的virtualenv

例如你从Gitlab仓库里clone了项目组的刀刀同学编写的测试脚本到你自己的电脑里，你想修改些东西，并调试，咋办？可以通过下面的操作快速创建 VirtualEnv

```
cd <repository>pip install -e .
```

This will set up a symlink to your code in site-packages, allowing you to edit your code while
your tests run against it as if it were installed.
Setting up your project in development mode lets you avoid having to reinstall every time you want to run your tests,
and is less brittle than mucking about with sys.path to point your tests at local code.
Also consider using tox

##  

## 遇到的问题：

**问题：**
pytest可以输出覆盖率的html报告

使用命令如下：

```
pytest -vv --cov=./ --cov-report=htmlopen htmlcov/index.html 
```

有可能遇到报错：

```
(venv) zhangxiaofans-MacBook-Pro:mgap-mendel joe
$ pytest --cov-report=htmlusage: 
pytest [options] [file_or_dir] [file_or_dir] [...]
pytest: 
error: unrecognized 
arguments: --cov-report=html  
inifile: None  
rootdir: /Users/joe/workspace/platform/mgap-mendel/mgap-mendel
```

原因：
缺少pytest cov的包

解决方法


```
pip install pytest-cov
```

 

 

 

 

 