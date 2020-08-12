# [python字典及其内置函数详解]

字典是另一种可变容器模型，且可存储任意类型对象。

字典的每个键值(key=>value)对用冒号(**:**)分割，每个对之间用逗号(**,**)分割，整个字典包括在花括号(**{})**中 ,格式如下所示：

![img](https://images2018.cnblogs.com/blog/1240023/201807/1240023-20180710212651094-957452138.png)

键必须是唯一的，但值则不必。

值可以取任何数据类型，但键必须是不可变的，如字符串，数字或元组。

一个简单的字典实例：

![img](https://images2018.cnblogs.com/blog/1240023/201807/1240023-20180710212756305-1217637424.png)

访问字典里的值

![img](https://images2018.cnblogs.com/blog/1240023/201807/1240023-20180710212838807-710537882.png)

如果用字典里不存在的键访问会出现错误

修改字典

![img](https://images2018.cnblogs.com/blog/1240023/201807/1240023-20180710212959716-1322553069.png)

删除字典

![img](https://images2018.cnblogs.com/blog/1240023/201807/1240023-20180710213033011-39279197.png)

字典键的特性

字典值可以是任何的 python 对象，既可以是标准的对象，也可以是用户定义的，但键不行。

两个重要的点需要记住：

![img](https://images2018.cnblogs.com/blog/1240023/201807/1240023-20180710213132129-378438009.png)

### 字典的内置函数和方法

#### 内置函数：

![img](https://images2018.cnblogs.com/blog/1240023/201807/1240023-20180710213255673-1609837291.png)

#### 内置方法

1.Python 字典 clear() 函数用于删除字典内所有元素。

![img](https://images2018.cnblogs.com/blog/1240023/201807/1240023-20180710213407884-625645995.png)

2.Python 字典 copy() 函数返回一个字典的浅复制。

![img](https://images2018.cnblogs.com/blog/1240023/201807/1240023-20180710213449093-1807854146.png)

3.Python 字典 fromkeys() 函数用于创建一个新字典，以序列seq中元素做字典的键，value为字典所有键对应的初始值。

![img](https://images2018.cnblogs.com/blog/1240023/201807/1240023-20180710213526803-1967709738.png)

4.Python 字典 get() 函数返回指定键的值，如果值不在字典中返回默认值。

![img](https://images2018.cnblogs.com/blog/1240023/201807/1240023-20180710213605798-1142672209.png)

5.Python 字典 in 操作符用于判断键是否存在于字典中，如果键在字典dict里返回true，否则返回false。

![img](https://images2018.cnblogs.com/blog/1240023/201807/1240023-20180710213637347-1856271250.png)

6.Python 字典 items() 方法以列表返回可遍历的(键, 值) 元组数组。

![img](https://images2018.cnblogs.com/blog/1240023/201807/1240023-20180710213715012-1571953966.png)

7.Python 字典 keys() 方法以列表返回一个字典所有的键。

![img](https://images2018.cnblogs.com/blog/1240023/201807/1240023-20180710213747350-959419563.png)

8.Python 字典 setdefault() 方法和[get()方法](http://www.runoob.com/python3/python3-att-dictionary-get.html)类似, 如果键不已经存在于字典中，将会添加键并将值设为默认值。

![img](https://images2018.cnblogs.com/blog/1240023/201807/1240023-20180710213824030-813618746.png)

9.Python 字典 update() 函数把字典参数 dict2 的 **key/value(键/值)** 对更新到字典 dict 里。

![img](https://images2018.cnblogs.com/blog/1240023/201807/1240023-20180710213853444-251213080.png)

10.Python 字典 values() 方法以列表返回字典中的所有值。

![img](https://images2018.cnblogs.com/blog/1240023/201807/1240023-20180710213932660-765660633.png)

11.Python 字典 pop() 方法删除字典给定键 key 所对应的值，返回值为被删除的值。key值必须给出。 否则，返回default值

![img](https://images2018.cnblogs.com/blog/1240023/201807/1240023-20180710214004749-1203842739.png)

12.Python 字典 popitem() 方法随机返回并删除字典中的一对键和值(一般删除末尾对)。

如果字典已经为空，却调用了此方法，就报出KeyError异常。

![img](https://images2018.cnblogs.com/blog/1240023/201807/1240023-20180710214036683-13943043.png)

 

 