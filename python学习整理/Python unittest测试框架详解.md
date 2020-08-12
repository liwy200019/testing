# unittest测试框架详解

## ***1\***|***0\*****unittest单元测试的定义

###  ***1\***|***1\*****1.什么是单元测试？**



单元测试是指，对软件中的最小可测试单元在与程序其他部分相隔离的情况下进行检查和验证的工作，这里的最小可测试单元通常是指函数或者类，一般是开发来做的，按照测试阶段来分，就是单元测试、集成测试、系统测试以及验收测试。 

###  ***1\***|***2\*****2.为什么要做单元测试？**

- 📕 单元测试之后，才是集成测试，单个单个的功能模块测试通过之后，才能把单个功能模块集成起来做集成测试，为了从底层发现bug，单元测试时可以减少合成后出现的问题。
- 📒 越早发现bug越好，这样可以早点发现问题，不然问题累计到后面，很可能会因为一个做错了而导致整个模块甚至更大范围的推倒重来，对于时间和经费来说，是非常浪费的！
- 📘 对于测试来说，单元测试就是为了执行用例，输入测试数据--》输出测试结果

## ***2\***|***0\*****unittest测试框架及原理

 做过自动化测试的同学应该都知道python中的unittest框架，它是python自带的一套测试框架，学习起来也相对较容易，unittest框架最核心的四个概念： 

🍊 test case：就是我们的测试用例，unittest中提供了一个基本类TestCase，可以用来创建新的测试用例，一个TestCase的实例就是一个测试用例；unittest中测试用例方法都是以test开头的，且执行顺序会按照方法名的ASCII值排序。

 🍅 test fixure：测试夹具，用于测试用例环境的搭建和销毁。即用例测试前准备环境的搭建（SetUp前置条件），测试后环境的还原（TearDown后置条件），比如测试前需要登录获取token等就是测试用例需要的环境，运行完后执行下一个用例前需要还原环境，以免影响下一条用例的测试结果。

 🍋 test suite：测试套件，用来把需要一起执行的测试用例集中放到一块执行，相当于一个篮子。我们可以使用TestLoader来加载测试用例到测试套件中。

 🍇 test runner：用来执行测试用例的，并返回测试用例的执行结果。它还可以用图形或者文本接口，把返回的测试结果更形象的展现出来，如：HTMLTestRunner。

## ***3\***|***0\*****unittest的断言**

| **方法**                   | **检查**            |
| :------------------------- | :------------------ |
| assertEqual(a, b,msg=None) | a ==b               |
| assertNotEqual(a, b)       | a !=b               |
| assertTrue(x)              | bool(x) is True     |
| assertFalse(x)             | Bool(x) is False    |
| assertIs(a, b)             | a is b              |
| assertIsNot(a, b)          | a is not b          |
| assertIsNone(x)            | x is None           |
| assertIsNotNone(x)         | x is not None       |
| assertIn(a, b)             | a in b              |
| assertNotIn(a, b)          | a not in b          |
| assertIsInstance(a, b)     | isinstance(a,b)     |
| assertNotIsInstance(a, b)  | not isinstance(a,b) |

 如果断言失败即不通过就会抛出一个`AssertionError`断言错误，成功则标识为通过，以上几种方式都有一个共同点，就是都有一个msg参数（表中只列了一个，其实都有），默认是None，即`msg = None`，如果指定msg参数的值，则将该信息作为失败的错误信息返回。

## ***4\***|***0\*****TestCase测试用例**

- 导入unittest模块
- 创建一个测试类，并继承`unittest.TestCase()`
- 定义测试方法，方法名必须以test_开头
- 调用`unittest.main()`方法来运行测试用例，unittest.main()方法会搜索该模块下所有以test开头的测试用例方法，并自动执行

下面以注册功能为例，这个register.py就是注册功能的代码，没有前端界面，功能比较简单，只是方便用于演示，直接导入就可以使用。



```
# register.py
users = [{'username': 'test', 'password': '123456'}]


def register(username, password1, password2):

    if not all([username, password1, password2]):
        return {"code": 0, "msg": "所有参数不能为空"}
    # 注册功能
    for user in users:
        if username == user['username']:
            return {"code": 0, "msg": "该用户名已存在！"}
    else:
        if password1 != password2:
            return {"code": 0, "msg": "两次密码输入不一致！"}
        else:
            if 6 <= len(username) >= 6 and 6 <= len(password1) <= 18:
                users.append({'username': username, 'password': password2})
                return {"code": 1, "msg": "注册成功"}
            else:
                return {"code": 0, "msg": "用户名和密码必须在6-18位之间"}
```

 下面是编写测试用例例子：



```
# test_register.py
import unittest
from register import register   # 导入被测试的代码


class TestRegister(unittest.TestCase):
    """注册接口测试用例类"""

    def test_register_success(self):
        """注册成功"""
        data = ("mikitest", "miki123", "miki123")   # 测试数据
        expected = {"code": 1, "msg": "注册成功！"}   # 预期结果
        result = register(*data)    # 把测试数据传到被测的代码，接收实际结果
        self.assertEqual(expected, result)  # 断言，预期和实际是否一致，一致即用例通过

    def test_username_isnull(self):
        """注册失败-用户名为空"""
        data = ("", "miki123", "miki123")
        expected = {"code": 0, "msg": "所有参数不能为空！"}
        result = register(*data)
        self.assertEqual(expected, result)

    def test_username_lt6(self):
        """注册失败-用户名大于18位"""
        data = ("mikitestmikitestmikitest", "miki123", "miki123")
        expected = {"code": 0, "msg": "用户名和密码必须在6-18位之间！"}
        result = register(*data)
        self.assertEqual(expected, result)	# 这条用例应该是不通过的，注册代码bug

    def test_pwd1_not_pwd2(self):
        """注册失败-两次密码不一致"""
        data = ("miki123", "test123", "test321")
        expected = {"code": 0, "msg": "两次密码输入不一致！"}
        result = register(*data)
        self.assertEqual(expected, result)


# 如果直接运行这个文件，需要使用unittest中的main函数来执行测试用例
if __name__ == '__main__':
    unittest.main()
```

 上面传递测试数据处用到一个*解包，我在python基础中有讲过解包的原理和例子，不明白的可以往回翻看一下，传送门：https://www.cnblogs.com/miki-peng/p/12229199.html

 测试用例运行结果如下，一共4条用例，其中通过3条，不通过1条，不通过的是本身注册代码的bug。



```
Testing started at 21:58 ...
C:\software\python\python.exe "C:\Program Files\JetBrains\PyCharm Community Edition 2019.1.3\helpers\pycharm\_jb_unittest_runner.py" --target test_register.TestRegister
Launching unittests with arguments python -m unittest test_register.TestRegister in D:\learn\python_test


{'code': 1, 'msg': '注册成功！'} != {'code': 0, 'msg': '用户名和密码必须在6-18位之间！'}

Expected :{'code': 0, 'msg': '用户名和密码必须在6-18位之间！'}
Actual   :{'code': 1, 'msg': '注册成功！'}
<Click to see difference>

Traceback (most recent call last):
  File "C:\Program Files\JetBrains\PyCharm Community Edition 2019.1.3\helpers\pycharm\teamcity\diff_tools.py", line 32, in _patched_equals
    old(self, first, second, msg)
  File "C:\software\python\lib\unittest\case.py", line 839, in assertEqual
    assertion_func(first, second, msg=msg)
  File "C:\software\python\lib\unittest\case.py", line 1138, in assertDictEqual
    self.fail(self._formatMessage(msg, standardMsg))
  File "C:\software\python\lib\unittest\case.py", line 680, in fail
    raise self.failureException(msg)
AssertionError: {'code': 0, 'msg': '用户名和密码必须在6-18位之间！'} != {'code': 1, 'msg': '注册成功！'}
- {'code': 0, 'msg': '用户名和密码必须在6-18位之间！'}
+ {'code': 1, 'msg': '注册成功！'}

During handling of the above exception, another exception occurred:

Traceback (most recent call last):
  File "C:\software\python\lib\unittest\case.py", line 59, in testPartExecutor
    yield
  File "C:\software\python\lib\unittest\case.py", line 615, in run
    testMethod()
  File "D:\learn\python24\python_base\day13_task\test_register.py", line 36, in test_username_lt6
    self.assertEqual(expected, result)


Ran 4 tests in 0.007s

FAILED (failures=1)

Process finished with exit code 1

Assertion failed
```

## ***5\***|***0\*****TestFixure测试夹具**



```
# test_register.py
import unittest
from register import register   # 导入被测试的代码


class TestRegister(unittest.TestCase):
    """注册接口测试用例类"""
    
    def setUp(self):
        # 每条用例执行之前都会执行
        print("用例{}开始执行--".format(self))

    def tearDown(self):
        # 每条用例执行之后都会执行
        print("用例{}执行结束--".format(self))

    @classmethod	# 指明这是个类方法以类为维度去执行的
    def setUpClass(cls):
        # 整个测试用例类中的用例执行之前，会先执行此方法
        print("-----setup---class-----")

    @classmethod
    def tearDownClass(cls):
        # 整个测试用例类中的用例执行完之后，会执行此方法
        print("-----teardown---class-----")

    def test_register_success(self):
        """注册成功"""
        data = ("mikitest", "miki123", "miki123")   # 测试数据
        expected = {"code": 1, "msg": "注册成功！"}   # 预期结果
        result = register(*data)    # 把测试数据传到被测的代码，接收实际结果
        self.assertEqual(expected, result)  # 断言，预期和实际是否一致，一致即用例通过

    def test_username_isnull(self):
        """注册失败-用户名为空"""
        data = ("", "miki123", "miki123")
        expected = {"code": 0, "msg": "所有参数不能为空！"}
        result = register(*data)
        self.assertEqual(expected, result)


# 如果直接运行这个文件，需要使用unittest中的main函数来执行测试用例
if __name__ == '__main__':
    unittest.main()
```

 运行结果：



```
Testing started at 22:19 ...
C:\software\python\python.exe "C:\Program Files\JetBrains\PyCharm Community Edition 2019.1.3\helpers\pycharm\_jb_unittest_runner.py" --path D:/learn/python/test_register.py
Launching unittests with arguments python -m unittest D:/learn/python/test_register.py in D:\learn\python
-----setup---class-----用例test_pwd1_not_pwd2 (test_register.RegisterTestCase)开始执行--
用例test_pwd1_not_pwd2 (test_register.RegisterTestCase)执行结束--
用例test_register_success (test_register.RegisterTestCase)开始执行--
用例test_register_success (test_register.RegisterTestCase)执行结束--
用例test_username_isnull (test_register.RegisterTestCase)开始执行--
用例test_username_isnull (test_register.RegisterTestCase)执行结束--
用例test_username_lt6 (test_register.RegisterTestCase)开始执行--
用例test_username_lt6 (test_register.RegisterTestCase)执行结束--
-----teardown---class-----

Ran 4 tests in 0.003s

OK

Process finished with exit code 0
```

## ***6\***|***0\*****TestSuite测试套件**

- 🍊 unittest.TestSuite()
  - `addTest()`：添加单个测试用例方法
  - `addTest([..])`：添加多个测试用例方法，方法名存在一个列表
- 🍊 unittest.TestLoader()
  - `loadTestsFromTestCase(测试类名)`：添加一个测试类
  - `loadTestsFromModule(模块名)`：添加一个模块
  - `discover(测试用例的所在目录)`：指定目录去加载，会自动寻找这个目录下所有符合命名规则的测试用例



```
# run_test.py，与test_register.py、register.py同一目录下
import unittest
import test_register

# 第一步，创建一个测试套件
suite = unittest.TestSuite()

# 第二步：将测试用例，加载到测试套件中
# 方式1，添加单条测试用例
# case = test_register("test_register_success")	# 创建一个用例对象，注意：通过用例类去创建测试用例对象的时候，需要传入用例的方法名（字符串类型）
# suite.addTest(case)	# 添加用例到测试套件中

# 方式2，添加多条测试用例
# case1 = test_register("test_register_success")
# case2 = test_register("test_username_isnull")
# suite.addTest([case1, case2])	# 添加用例到测试套件中

# 方式3，添加一个测试用例类
# loader = unittest.TestLoader()	# 创建一个加载对象
# suite.addTest(loader.loadTestsFromTestCase(test_register.TestRegister))

# 方式4，添加一个模块
loader = unittest.TestLoader()	# 创建一个加载对象
suite.addTest(loader.loadTestsFromModule(test_register))

# 方式5，指定测试用例的所在的目录路径，进行加载
# loader = unittest.TestLoader()
# suite.addTest(loader.discover(r"d:\learn\python"))
```

 通常我们使用方式4、5比较多，你可以根据实际情况来运用。其中方式5，还可以自定义匹配规则，默认是会寻找目录下`test*.py`文件，即所有以test开头命名的py文件，自定义如下：



```
loader = unittest.TestLoader()
suite.addTest(loader.discover(start_dir = r"d:\learn\python", pattern="test_case*.py"))		# 匹配规则：所有以test_case开头的
```

## ***7\***|***0\*****TestRunner执行用例**

 html格式的就是HTMLTestRunner了，`HTMLTestRunner`是 Python 标准库的 unittest 框架的一个扩展，它可以生成一个直观清晰的 HTML 测试报告。使用的前提就是要下载 HTMLTestRunner.py，下载完后放在python的安装目录下的scripts目录下即可。

 text文本相对于html来说过于简陋，与控制台输出的没有什么区别，也几乎没有人使用，这里不作演示，使用方法是一样的。我们结合前面的测试套件来演示一下如何生成html格式的测试报告：



```
# run_test.py，与test_register.py、register.py同一目录下
import unittest
import test_register
from HTMLTestRunner import HTMLTestRunner

# 创建测试套件
suite = unittest.TestSuite()

# 通过模块加载测试用例
loader = unittest.TestLoader()
suite.addTest(loader.loadTestsFromModule(test_register))

# 创建测试运行程序启动器
runner = HTMLTestRunner(stream=open("report.html", "wb"),  # 打开一个报告文件，将句柄传给stream
                        tester="miki",                    # 报告中显示的测试人员
                        description="注册接口测试报告",        # 报告中显示的描述信息
                        title="自动化测试报告")                 # 报告的标题

# 使用启动器去执行测试套件里的用例
runner.run(suite)
```

 **相关参数说明：**

- `stream`：指定输出的方式

- `tester`：报告中要显示的测试人员的名字

- `description`：报告中要显示的面熟信息

- `title`：测试报告的标题

- ```
  verbosity
  ```

   

  ：表示测试报告信息的详细程度，一共三个值，默认是2

  - 0 (静默模式)：你只能获得总的测试用例数和总的结果，如：总共100个 失败10 成功90
  - 1 (默认模式)：类似静默模式，只是在每个成功的用例前面有个. 每个失败的用例前面有个F
  - 2 (详细模式)：测试结果会显示每个测试用例的所有相关的信息

 运行完毕，你会发现你的项目目录下已经生成了一个report.html文件，在浏览器中打开，就可以查看测试报告了。

[![image-20200315232546384](https://images.cnblogs.com/cnblogs_com/miki-peng/1671265/o_200315163418image-20200315232546384.png)](https://images.cnblogs.com/cnblogs_com/miki-peng/1671265/o_200315163418image-20200315232546384.png)