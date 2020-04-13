# Python类定义

Python的历史中有两种类定义，分别是经典类和新式类。

经典类和新式类最明显的区别在于多继承搜索的路径。

经典类多继承搜索顺序采用的是深度优先规则，而新式类多继承搜索顺序采用的是广度优先规则。

1. 在Python2中

   默认的类定义是经典类，只有显式继承了object基类才是新式类，即：

   ```python
   class Person(object): pass   # 新式类写法
   class Person(): pass 				# 经典类写法
   class Person: pass 					 # 经典类写法
   ```

2. 在Python3中

   Python3取消了经典类，默认都是新式类，因此三种写法没有区别：

   ```python
   class Person(object): pass   # 新式类写法
   class Person(): pass 				# 新式类写法
   class Person: pass 					 # 新式类写法
   ```

   但是如果项目会涉及到和Python2的兼容，那么推荐第一种写法。