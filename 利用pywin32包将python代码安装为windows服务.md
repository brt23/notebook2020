# 利用pywin32包将python代码安装为windows服务

1. 第一坑

   当安装为服务后，启动时报错需要在windows日志中查看

   当报win32service, win32api, win32con, winerror ImportError: DLL load failed时

   将anaconda3\envs\pytorch\Lib\site-packages\pywin32_system32这样的目录下的两个dll文件

   pythoncom37.dll

   pywintypes37.dll

   复制到C:\windows\system32下可以解决这个问题
   
2. 不使用pip安装pywin32使用conda安装pywin32也能解决问题