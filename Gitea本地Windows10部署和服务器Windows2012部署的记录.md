# Gitea本地Windows10部署和服务器Windows2012部署的记录

## Gitea本地Windows10部署

### 准备

1. 下载gitea、git、nginx的windows安装包
2. [gitea官网](https://gitea.io/en-us/)
3. [git官网](https://git-scm.com/)
4. [nginx官网](http://nginx.org/en/download.html)

### 安装

1. 安装git，直接双击下载好的git for windows的exe安装包进行安装
2. **新建**一个用于存放和执行gitea的**文件夹**，该文件夹可以放到用于喜欢的任意位置
3. 将gitea.exe文件放入第2步建好的文件夹中
4. 将nginx压缩包解压的文件夹放入第2步建好的文件夹中

### 配置gitea

1. 启动gitea，直接双击gitea.exe文件；启动后，gitea会弹出一个**终端窗口**

2. 当gitea的终端窗口出现 *Starting new server: tcp:0.0.0.0:3000 on PID: xxxx* ，并且窗口信息**没有刷新时**，在浏览器中输入http://localhost:3000打开gitea的web界面

3. 在gitea的web界面中点击注册或者登录，gitea的首次注册或登录操作会自动进入gitea的**初始配置界面**

4. **初始配置界面**包含**数据库设置**、**一般设置**、**可选设置**这三部分

   1. 数据库设置

      数据库支持：MySQL、PostgreSQL、MSSQL、SQLite3

      只有SQLite3数据库已经包含在是gitea.exe中，因此不需要另外安装和配置，在快速部署的目标下建议选择SQLite3作为gitea的数据库

   2. 一般设置

      主要是仓库、url等路径设置，默认设置就能满足需求

      要特别说明的是，**仓库根目录**会默认在C盘，而我一般会将该目录设定到gitea所在的文件夹里面，我认为这样会方便移植

   3. 可选设置

      其中最常用的是管理员账号设置，如果**初始配置界面**中不设置管理员，那么**第一个注册用户**会自动拥有管理员权限

5. 不知道是不是当前版本的bug，在点击**初始配置界面**最下面的立即安装按钮之前，请在gitea所在的文件夹中新建一个data文件夹，该文件夹将会存放数据库文件

6. 点击**初始配置界面**的**立即安装按钮**，gitea终端会开始出现新的信息；当安装完成时，gitea的web界面会自动跳转到管理员账号的登录页面中（如果没有在初始配置界面中设置管理员，应该会跳转到gitea的首页）

### 配置nginx反向代理

nginx的反向代理可以将IP访问替换成url访问，方便用户记忆gitea的访问地址

1. nginx配置文件在nginx文件夹中"conf\nginx.conf"
2. 向nginx.conf文件中添反向代理配置，在http块中添加新的server块，**如下所示**
3. 通过双击nginx.exe或者终端中输入start .\nginx.exe 启动nginx
4. 通过终端中输入.\nginx.exe -s stop 停止和关闭nginx

```yaml
#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    server {
        listen       80;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   html;
            index  index.html index.htm;
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }
	
	# 新添加的server块
	# 其他部分都是默认的配置文件
	# 该新添加的server块中有两个关键参数：
	# server_name  git.example.com; # 为浏览器中输入的网址
	# proxy_pass http://127.0.0.1:3000; # 为网址映射的实际ip地址和端口号
	# 最终实现的效果就是在浏览器中输入http://git.example.com
	# 而实际访问的是http://127.0.0.1:3000
	server {
        listen       80;
        server_name  git.example.com;

        proxy_set_header X-Forwarded-Host $host;
        proxy_set_header X-Forwarded-Server $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

        location / {
            proxy_pass http://127.0.0.1:3000;
            proxy_connect_timeout 600;
            proxy_read_timeout 600;
        }
    }


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}

}
```

5. 除了nginx配置文件需要更改，gitea的配置文件也需要配合更改，gitea的配置文件在gitea.exe所在的文件夹中的"custom\conf\app.ini"，修改如下：

```python
[server]
SSH_DOMAIN       = localhost
DOMAIN           = localhost
HTTP_PORT        = 3000
ROOT_URL         = http://127.0.0.1:3000/ # 将这个URL改为和nginx中proxy_pass相同的URL
DISABLE_SSH      = false
SSH_PORT         = 22
LFS_START_SERVER = true
LFS_CONTENT_PATH = D:/GitLocalServer/data/lfs
LFS_JWT_SECRET   = -v9_qlVNStypC4t5IQO0ZBWTmrTf3PaWpaNMudm6qHg
OFFLINE_MODE     = false
```

### hosts映射IP和URL

要想实现URL访问gitea，还需要告诉浏览器URL对应哪个IP地址，就无法通过URL访问到gitea的web界面。在本机要实现IP和URL的捆绑可以通过hosts实现。

1. 打开windows的hosts文件(C:\Windows\System32\drivers\etc)，在最下面的空白处添加：

```python
127.0.0.1 git.example.com
```

2. 保存后关闭hosts文件

### 完成

至此，完成gitea在本地windows计算机上的配置工作，该例子可以实现本机用户通过http://git.example.com 访问gitea的web界面。

## Gitea服务器Windows2012部署

主要步骤和本地部署完全一样，**不一样的是**：

### 配置nginx反向代理

需要将反向代理中proxy_pass改为服务器的局域网IP地址

假设服务器的局域网IP地址为192.168.0.199：

```yaml
# 新添加的server块
	# 其他部分都是默认的配置文件
	# 该新添加的server块中有两个关键参数：
	# server_name  git.example.com; # 为浏览器中输入的网址
	# proxy_pass http://192.168.0.199:3000; # 为网址映射的实际ip地址和端口号
	# 最终实现的效果就是在浏览器中输入http://git.example.com
	# 而实际访问的是http://192.168.0.199:3000
	server {
        listen       80;
        server_name  git.example.com;

        proxy_set_header X-Forwarded-Host $host;
        proxy_set_header X-Forwarded-Server $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

        location / {
            proxy_pass http://192.168.0.199:3000;
            proxy_connect_timeout 600;
            proxy_read_timeout 600;
        }
    }
```

gitea的配置文件也需要配合更改：

```python
[server]
SSH_DOMAIN       = localhost
DOMAIN           = localhost
HTTP_PORT        = 3000
ROOT_URL         = http://192.168.0.199:3000/ # 将这个URL改为和nginx中proxy_pass相同的URL
DISABLE_SSH      = false
SSH_PORT         = 22
LFS_START_SERVER = true
LFS_CONTENT_PATH = D:/GitLocalServer/data/lfs
LFS_JWT_SECRET   = -v9_qlVNStypC4t5IQO0ZBWTmrTf3PaWpaNMudm6qHg
OFFLINE_MODE     = false
```

### DNS服务器映射IP和URL

在windows2012的DNS服务器中，添加一个新的主机，该主机将192.168.0.199和http://git.example.com实现捆绑。

### 完成

至此，完成gitea在windows服务器上的配置工作，该例子可以实现局域网中的用户通过http://git.example.com 访问gitea的web界面。

### 注意

nginx会监听80端口，如果服务器上有程序占用了80端口会到只nginx打不开，这时要么修改nginx监听端口（推荐），要么修改服务器上的程序

## 将gitea部署为windows服务

### Prerequisites
The following changes are made in C:\gitea\custom\conf\app.ini:

```
RUN_USER = COMPUTERNAME$
```


Sets Gitea to run as the local system user.

COMPUTERNAME is whatever the response is from echo %COMPUTERNAME% on the command line. If the response is USER-PC then RUN_USER = USER-PC$

### Use absolute paths
If you use sqlite3, change the PATH to include the full path:

```
[database]
PATH     = c:/gitea/data/gitea.db
```

### Register as a Windows Service
To register Gitea as a Windows service, open a command prompt (cmd) as an Administrator, then run the following command:

```
sc.exe create gitea start= auto binPath= "\"C:\gitea\gitea.exe\" web --config \"C:\gitea\custom\conf\app.ini\""
```


Do not forget to replace C:\gitea with the correct Gitea directory.

Open “Windows Services”, search for the service named “gitea”, right-click it and click on “Run”. If everything is OK, Gitea will be reachable on http://localhost:3000 (or the port that was configured).

### Unregister as a service
To unregister Gitea as a service, open a command prompt (cmd) as an Administrator and run:

```
sc.exe delete gitea
```

