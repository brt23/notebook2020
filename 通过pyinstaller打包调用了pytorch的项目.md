# 通过pyinstaller打包调用了pytorch的项目

1. 报 could not get source code 错误

   安装pytorch==1.1.0 torchvision==0.2.2 就能解决这个问题，这个应该是pyinstaller和新版的pytorch不兼容，只能通过降低版本来解决问题

2. PIL找不到DLL

   这个可以通过切换pillow的版本来解决问题

3. pyi_rth_pkgres报错

   安装最新版的pyinstaller可以解决