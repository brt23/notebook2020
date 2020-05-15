# Python在Windows环境下利用pyodbc包登录MsSQL

## 预备工作

1. 安装pyodbc包

   ```
   pip install pyodbc
   ```

2. 确定本机安装的ODBC drivers for SQL Server驱动是什么

   Windows管理工具-》ODBC data sources -》驱动程序 

   在该面板下能查看本机已经安装了什么ODBC驱动

   推荐驱动：ODBC Driver 17 for SQL Server

## Python调用

```python
import pyodbc

    server = r'192.168.0.112\sqlexpress' # 实例名称
    database = 'TestDb' # 数据库名称
    username = 'sa' # 用户名
    password = '123456' # 密码
    command = f'DRIVER={{ODBC Driver 17 for SQL Server}};SERVER={server};DATABASE={database};UID={username};PWD={password}'
    cnxn = pyodbc.connect(command)
    cursor = cnxn.cursor()
```

