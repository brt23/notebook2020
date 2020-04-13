# Python 使用\__getattr__ 和getatter两个方法联合可以快速访问内部对象的方法

在查询委托机制的python实现时，偶然间发现了如下代码：

```python
class Wrapper:

    def __init__(self, obj):
        self.obj = obj

    def __getattr__(self, item):
        print(type(item))
        print(f'{item}')
        return getattr(self.obj, item)
    
if __name__ == '__main__':
        wap = Wrapper([1, 2, 3])
        wap.append(1)
        print(wap)
```

这个Wrapper类对象居然就能直接像列表一样使用，并能直接调用类表的方法。于是对这几行代码进行了深度的研究，基本理解了其表层的运行机制。

1. \__getattr__提供了Wrapper类Wrapper.attr这样获取属性的功能

   该方法实际获取的item会被翻译成一个字符串

   如上代码中的item就是'append'

2. getattr会返回self.obj中的append方法

   可以看成这样的操作：func = wap.append

3. wap.append == self.obj.append 在对该方法进行调用

   可以看成这样的操作：func(1)

4. 于是就完成了Wrapper对象内部list的添加操作

将该代码扩展一下：

```python
class Wrapper:

    def __init__(self, obj):
        self.obj = obj

    def __getitem__(self, key):
        print(type(key))
        print(f'{key}')
        return getattr(self.obj, key)

    def __getattr__(self, item):
        print(type(item))
        print(f'{item}')
        return getattr(self.obj, item)

    def __repr__(self):
        return f"<class 'Warpper'> {type(self.obj)} {repr(self.obj)}"

if __name__ == '__main__':
    wap = Wrapper([1, 2, 3])
    wap['append'](1)
    wap.append(1)
    print(wap)
```

也可以通过下标访问属性的方式来完成这样的操作。