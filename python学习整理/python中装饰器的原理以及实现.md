# python中装饰器的原理以及实现

## 

## 1.python的装饰器说白了就是闭包函数的一种应用场景，在运用的时候我们遵循

```
#开放封闭原则:对修改封闭，对拓展开放
```

## 2.什么是装饰器

```
#装饰他人的器具，本身可以是任意可调用的对象，被装饰者也可以是任意可调用对象
#装饰器的原则：1.不可修改被装饰对象的源代码，2不修改被装饰对象的调用方式
#装饰器的目标：在遵循1和2的前提下，为被装饰对象添加上新功能
```

## 3.实现装饰器之前先来了解闭包函数

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
#1.闭包函数 => 函数嵌套+函数对象+名称空间与作用域
#闭：定义是在函数内的函数
#包：该内部函数需要访问一个名字，该名字是属于外层函数作用域的

#闭包函数实现的基本模板

def outter(xxx):
     def inner()
           xxx
      return inner

#为函数体传参的两个方案
#1.直接传入参数
#2.在函数体内部定义变量
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

## 4.装饰器的实现

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
#阶段一
#计算一个函数执行的时间

import time
def index():
      time.sleep(1)
      print("welcome to my world")

start = time.time()
index()
end = time.time()
print("run time is %s" %(end - start))
#修改了装饰对象的源代码

#阶段二
import time
def index():
      time.sleep(1)
      print("welcome to my world")

def wrapper():
      start = time.time()
      index()
      end = time.time()
      print("run time is %s" %(end - start))
#修改了装饰对象的调用方式

#阶段三
import time
def index():
      time.sleep(1)
      print("welcome to my world")

def wrapper(func):
      start = time.time()
      func()
      end = time.time()
      print("run time is %s" %(end - start))
wrapper(index)
#修改了装饰对象的调用方式

#阶段四:
import time

def index():
    time.sleep(1)
    print('welcome to index page')

def timmer(func):
    # func=最原始那个index的内存地址
    def wrapper():
        start=time.time()
        func()
        stop=time.time()
        print('run time is %s' %(stop - start))
    return wrapper
#把函数作为参数传进去，返回一个函数再去执行，替换返回的函数名为之前传入的函数 
index=timmer(index) #index=wrapper的内存地址
index() #wrapper()
#这样就没有修改被装饰对象的源代码和调用方式
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

## 5.装饰器语法糖

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
#装饰器语法糖
#在被装饰器对象上方单独一行写：@装饰器的名称
解释器一旦执行到@装饰器的名字就会执行 原函数名 = 装饰器的名字(正下方的那个函数)

#修订之后的装饰器
import time

def timmer(func):
    # func=最原始那个index的内存地址
    def wrapper(*args,**kwargs):
        start=time.time()        
        res=func(*args,**kwargs)
        stop=time.time()
        print('run time is %s' %(stop - start))
        return res
    return wrapper

@timmer #index=timmer(最原始那个index的内存地址) #index=wrapper的内存地址
def index():
    time.sleep(1)
    print('welcome to index page')

index()
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

## 6.完善装饰器

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
#装饰器实现函数，其函数对象的变化，其中包括一些内置的方法__name__,__doc__等等，
#这些都是要在被装饰之后返回同样的name，doc
下面我们引入一个第三方包 去实现这些功能
import time
from functools import wraps

def timmer(func):
    @wraps(func)
    def wrapper(*args,**kwargs):
        start=time.time()
        #函数返回值
        res=func(*args,**kwargs)
        stop=time.time()
        print('run time is %s' %(stop - start))
        return res
    # wrapper.__doc__=func.__doc__
    # wrapper.__name__=func.__name__
    return wrapper

# @timmer #index=timmer(最原始那个index的内存地址) #index=wrapper的内存地址
def index():
    """
    这是一个index函数
    :return:
    """
    time.sleep(1)
    print('welcome to index page')
#上面的引入的第三方包wraps，调用其装饰器帮我们实现了
    # wrapper.__doc__=func.__doc__
    # wrapper.__name__=func.__name__ 等等这些功能，这样能更好的满足用户的需求
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

## 7.上面实现的装饰器是无参装饰器，有参装饰器更新中…………

 装饰器这玩意挺有用，当时感觉各种绕，现在终于绕明白了，俺滴个大爷，还是要慢慢思考才能买明白各种的真谛，没事就来绕一绕

 

```
def outer(func):
	def inner():
		print("认证成功")
		result=func()
		print("登录成功")
		return result
	return inner
@outer
def OA():
	print("OA接口")
```

这里面需要注意的是：

- @outer和@outer()有区别，没有括号时，outer函数依然会被执行，这和传统的用括号才能调用函数不同，需要特别注意！
- 是OA这个函数名（而不是OA()这样被调用后）当做参数传递给装饰函数outer，也就是：func = OA，@outer等于outer(OA),实际上传递了OA的函数体，而不是执行OA后的返回值。
- outer函数return的是inner这个函数名，而不是inner()这样被调用后的返回值。

　1. 程序开始执行outer函数内部的内容，一开始它又碰到了一个函数，inner函数定义块被程序观察到后不会立刻执行，而是读入内存中。

　2. 再往下，碰到return inner，返回值是个函数名，并且这个函数名会被赋值给OA这个被装饰的函数，也就是OA = inner，此时OA函数被新的函数inner覆盖了（实际上是OA这个函数名更改成指向inner这个函数名指向的函数体内存地址，OA不再指向它原来的函数体的内存地址），再往后调用OA的时候将执行inner函数内的代码，而不是先前的函数体。那么先前的函数体去哪了？还记得我们将OA当做参数传递给func这个形参么？func这个变量保存了老的函数在内存中的地址，通过它就可以执行 老的函数体，你能在inner函数里看到result = func（）这句代码，它就是这么做的！

　3.接下来，还没有结束，依然通过OA()的方式调用OA 函数时，执行的就不再是老的OA函数的代码，而是inner函数的代码。在本例中，它首先会打印个“认证成功”的提示，然后，它会执行func函数并将返回值赋值个变量result，这个func函数就是老的OA函数；接着，它又打印了“登陆成功”的提示；最后返回result这个变量。我们可以用 r = OA()的方式接受result的值。

　4.仅仅是添加了一个装饰函数，就实现了我们的需求，在函数调用前先认证，调用后写入日志，这就是装饰器的最大作用。

