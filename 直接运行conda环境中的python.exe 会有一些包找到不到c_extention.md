# 直接运行conda环境中的python.exe 会有一些包找到不到c_extention

1. 可以通过在Python代码中添加C扩展的路径到环境变量中，一般C的扩展在Library\\bin目录下。该方法是临时添加环境变量，只在代码运行期间有效。

```python
os.environ['path'] += ';C:\\Users\\huangqingyu\\AppData\\Local\\Continuum\\anaconda3\\envs\\CRRC_running\\Library\\bin;'
```

