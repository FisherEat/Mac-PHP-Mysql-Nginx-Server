#### 搭建MacOSX+PHP+Nginx+MySql

*决定在Mac上搭建php开发环境，用来调试后端程序。走上开发道路一年多的日子里，我的iOS开发水平达到了中级水平，服务端的开发水平处于初级阶段，前端开发处于入门级别水平。对于前端开发， 熟悉javascript，html和css。*

*决定前往全栈开发的道路，后端熟悉并达到中级水平，使用php、node等开发；前端能到达中级水平，熟悉html+css+javascript；iOS达到高级水平，熟悉并且能够自己开发框架。*

*此处为配置MacOSX+PHP+Nginx+MySql开发环境。*

[TOC]



##### 参考文档

> 1. 安装文档：http://www.jb51.net/article/76759.htm
> 2. voer

##### 使用brew安装nginx

> ```shell
> $ brew search nginx 
> $ brew install nginx 
>
> Nginx安装路径
> $ cd /usr/local/etc/nginx
> $ ls -a
> .			koi-utf			scgi_params
> ..			koi-win			scgi_params.default
> fastcgi.conf		mime.types		uwsgi_params
> fastcgi.conf.default	mime.types.default	uwsgi_params.default
> fastcgi_params		nginx.conf		win-utf
> fastcgi_params.default	nginx.conf.default
>
> Nginx配置文件：
> nginx.conf 和 nginx.conf.default 为nginx的配置文件。
>
> 启动nginx
> $ sudo nginx 
> 关掉nginx进程
> $ sudo pkill nginx
> 查看localhost:8000
> 会执行www里的index.php文件
> ```

##### Nginx配置文件

> Nginx配置文件以及具体的配置信息如下：
>
> ```
>
> #user  nobody;
> worker_processes  1;
>
> #error_log  logs/error.log;
> #error_log  logs/error.log  notice;
> #error_log  logs/error.log  info;
>
> #pid        logs/nginx.pid;
>
>
> events {
>     worker_connections  1024;
> }
>
>
> http {
>     include       mime.types;
>     default_type  application/octet-stream;
>
>     #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
>     #                  '$status $body_bytes_sent "$http_referer" '
>     #                  '"$http_user_agent" "$http_x_forwarded_for"';
>
>     #access_log  logs/access.log  main;
>
>     sendfile        on;
> #tcp_nopush     on;
>
>     #keepalive_timeout  0;
>     keepalive_timeout  65;
>
>     #gzip  on;
>
>     server {
>         listen       80;
>         server_name  localhost;
>         #charset koi8-r;
>         #access_log  logs/host.access.log  main;
>
>         location / {
>             root   /usr/local/var/www;
>             index  index.php index.html index.htm;
>         }
>
>         #error_page  404              /404.html;
>
>         # redirect server error pages to the static page /50x.html
>         #
>         error_page   500 502 503 504  /50x.html;
>         location = /50x.html {
>             root   /usr/local/var/www;
>         }
>
>         # proxy the PHP scripts to Apache listening on 127.0.0.1:80
>           #
>         #location ~ \.php$ {
>         #    proxy_pass   http://127.0.0.1;
>         #}
>
>         # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
>
>         location ~ \.php$ {
>             root           /usr/local/var/www;
>             fastcgi_pass   127.0.0.1:9000;
>             fastcgi_index  index.php;
>             fastcgi_param  SCRIPT_FILENAME  /usr/local/var/www$fastcgi_script_name;
>             include        fastcgi_params;
>         }
>
>         # deny access to .htaccess files, if Apache's document root
>         # concurs with nginx's one
>         #
>         #location ~ /\.ht {
>         #    deny  all;
>         #}
>     }
>
>     # another virtual host using mix of IP-, name-, and port-based configuration
>     #
>     #server {
>     #    listen       8000;
>     #    listen       somename:8080;
>     #    server_name  somename  alias  another.alias;
>
>     #    location / {
>     #        root   html;
>     #        index  index.html index.htm;
>     #    }
>     #}
>
>     # HTTPS server
>     #
>     #server {
>     #    listen       443 ssl;
>     #    server_name  localhost;
>     #    ssl_certificate      cert.pem;
>     #    ssl_certificate_key  cert.key;
>     #    ssl_session_cache    shared:SSL:1m;
>     #    ssl_session_timeout  5m;
>      #    ssl_ciphers  HIGH:!aNULL:!MD5;
>     #    ssl_prefer_server_ciphers  on;
>
>     #    location / {
>     #        root   html;
>     #        index  index.html index.htm;
>     #    }
>     #}
> }
> include servers/*;
> ```
>
> Nginx.conf.default文件的配置如下：
>
> ```
> 其实我这里nginx.conf.default文件的配置和nginx.conf的配置一样的。是完全拷贝的两份。但是两份好像必须都配置，忧伤！
> ```
>
> over

##### 安装php-fpm

> Mac OSX 10.9以后的系统自带了PHP、php-fpm，省去了安装php-fpm的麻烦。 
> 这里需要简单地修改下php-fpm的配置，否则运行php-fpm会报错。
>
>
> ```
> <!-- lang: shell -->
>
> sudo cp /private/etc/php-fpm.conf.default /private/etc/php-fpm.conf
>
> vim /private/etc/php-fpm.conf
>
> ```
>
>
> 修改php-fpm.conf文件中的error_log项，默认该项被注释掉，这里需要去注释并且修改为error_log = /usr/local/var/log/php-fpm.log。如果不修改该值，运行php-fpm的时候会提示log文件输出路径不存在的错误。
>
> 直接在终端输入：
>
> ```shell
> 启动php
> $ sudo php-fpm 
>
> 干掉php进程()
> $ sudo pkill php-fpm
>
> ➜  ~ php-fpm 
> [14-Apr-2017 01:32:58] ERROR: failed to open error_log (/usr/local/var/log/php-fpm.log): Permission denied (13)
> [14-Apr-2017 01:32:58] ERROR: failed to post process the configuration
> [14-Apr-2017 01:32:58] ERROR: FPM initialization failed
> ➜  ~ sudo php-fpm 
> Password:
> [14-Apr-2017 01:33:06] ERROR: unable to bind listening socket for address '127.0.0.1:9000': Address already in use (48)
> [14-Apr-2017 01:33:06] ERROR: FPM initialization failed
> ➜  ~ sudo pkill php
> ➜  ~ sudo php-fpm 
> ➜  ~ nginx 
>
> $ lsof -i tcp:9000 
> $ 发现该指令并不起作用，必须用pkill强制删除php
> ```
>
> over

##### 安装pear

> pear是php第三方库的扩展程序，类似Python的pip和iOS的cocoapods。
>
> ```shell
> pear 是php 文档管理工具
> 在命令行里输入这些就可以安装pear。
> sudo php /usr/lib/php/install-pear-nozlib.phar
> sudo pear config-set php_ini /private/etc/php.ini
> sudo pecl config-set php_ini /private/etc/php.ini
> sudo pear upgrade-all
> ```
>
> 此处访问php的系统库，在MacOSX下没有权限，所以，需要：https://segmentfault.com/a/1190000004009133
>
> ```
> 重启Mac
> 在Mac重新启动起来以前按住Command+R键直到出现苹果图标为止
> 进入后桌面上会问你要恢复Mac还是进行磁盘急救，不用理会，直接在屏幕左上角的菜单栏里依次选择“实用工具-->终端”打开Mac自带的命令行工具
> 在终端里输入csrutil disable
> 改完后重启
> ```
>
> over

##### 安装mysql

> ```shell
> $ brew install mysql
> $ mysql server start 
> $ mysql.server stop
> 如果mysql server 无法启动的话，可以使用：
> $ mysqld 
> $ mysql
>
> 配置mysql
> 启动mysql
> $ mysqld  //改命令可以直接启动mysql
> $ mysql.server start 该命令也可以直接启动mysql
> 启动mysql命令行,该命令启动mysql后直接进入mysql命令行模式
> 若要进入该模式，必须先启动mysql
> $ mysql 
> //
> MySQL服务器启动错误 'The server quit without updating PID file'
> http://pein0119.github.io/2015/03/25/MySQL%E6%9C%8D%E5%8A%A1%E5%99%A8%E5%90%AF%E5%8A%A8%E9%94%99%E8%AF%AF-The-server-quit-without-updating-PID-file/
> 遇到该问题，我是使用这一条解决的：
> 1.我通过检查我的旧mac发现/usr/local/var/mysql/的所有者是gaolong
> 2.但是启动报错的那台mac检查发现/usr/local/var/mysql/的所有者是root
> 3.于是执行 sudo chown -R yxt /usr/local/var/mysql/  ytx为本台mac的用户名。
> 于是这个纠结了大半年的问题终于迎刃而解。
>
> 执行mysqld后：
>
> .....
> 2016-11-12 21:29:00 33202 [Note]   - '::' resolves to '::';
> 2016-11-12 21:29:00 33202 [Note] Server socket created on IP: '::'.
> 2016-11-12 21:29:00 33202 [Note] Event Scheduler: Loaded 0 events
> 2016-11-12 21:29:00 33202 [Note] mysqld: ready for connections.
> Version: '5.6.27'  socket: '/tmp/mysql.sock'  port: 3306  Homebrew
>
> 这段运行是多么优雅啊！
>
> 所以让我再次认识到服务器编程，除了是路径之外，还是权限！是路径+权限。
> 使用mysql.server start 启动
> bitbrothersdeMacBook-Pro-2:~ yxt$ mysql.server start 
> Starting MySQL
> . SUCCESS! 
> bitbrothersdeMacBook-Pro-2:~ yxt$ 
>
> ```

##### 实践

> *摘要：本实践是基于homebrew来搭建nginx单间nginx+mysql+php-fpm环境的。*
>
> 1. 安装homebrew
>
>    ```shell
>    <!-- lang: shell -->
>    $ ruby -e "$(curl -fsSL https://raw.github.com/Homebrew/homebrew/go/install)"
>
>    homebrew的常用命令：
>    $ brew update 
>    $ brew search pkg_name
>    $ brew install pkg_name 
>    ```
>
> 2. 安装nginx
>
>    安装
>
>    ```shell
>    $ brew search nginx
>    $ brew install nginx 
>    ```
>
>    配置
>
>    ```shell
>    <!-- lang: shell -->
>    $ cd /usr/local/etc/nginx/
>    $ mkdir conf.d
>    $ vim nginx.conf
>    $ vim ./conf.d/default.conf
>    本系统中有nginx.conf 和nginx.default.conf文件，因此不需要重新创建
>    ```
>
>    nginx.conf内容
>
>    ```
>    <!-- lang: shell -->
>    worker_processes  1;  
>
>    error_log       /usr/local/var/log/nginx/error.log warn;
>
>    pid        /usr/local/var/run/nginx.pid;
>
>    events {
>        worker_connections  256;
>    }
>
>    http {
>        include       mime.types;
>        default_type  application/octet-stream;
>
>        log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
>                          '$status $body_bytes_sent "$http_referer" '
>                          '"$http_user_agent" "$http_x_forwarded_for"';
>
>        access_log      /usr/local/var/log/nginx/access.log main;
>        port_in_redirect off;
>        sendfile        on; 
>        keepalive_timeout  65; 
>      #此处添加nginx配置文件
>        include /usr/local/etc/nginx/conf.d/*.conf;
>    }
>    ```
>
>    defaul.conf文件内容和配置
>
>    ```
>    <!-- lang: shell -->
>    server {
>        listen       8080;
>        server_name  localhost;
>
>        root /Users/user_name/nginx_sites/; # 该项要修改为你准备存放相关网页的路径
>
>        location / { 
>            index index.php;
>            autoindex on; 
>        }   
>
>        #proxy the php scripts to php-fpm  
>        #此处也需要配置
>        location ~ \.php$ {
>            include /usr/local/etc/nginx/fastcgi.conf;
>            fastcgi_intercept_errors on; 
>            fastcgi_pass   127.0.0.1:9000; 
>        }   
>    }
>    ```
>
>    over
>
> 3. 安装php-fpm
>
>    Mac OSX 10.9的系统自带了PHP、php-fpm，省去了安装php-fpm的麻烦。 这里需要简单地修改下php-fpm的配置，否则运行`php-fpm`会报错。
>
>    ```shell
>    <!-- lang: shell -->
>    sudo cp /private/etc/php-fpm.conf.default /private/etc/php-fpm.conf
>    vim /private/etc/php-fpm.conf
>    ```
>
>    修改php-fpm.conf文件中的`error_log`项，默认该项被注释掉，这里需要去注释并且修改为`error_log = /usr/local/var/log/php-fpm.log`。如果不修改该值，运行php-fpm的时候会提示log文件输出路径不存在的错误。
>
> 4. 安装mysql
>
>    安装
>
>    ```shell
>    <!-- lang: shell -->
>    brew install mysql
>    ```
>
>    常用命令
>
>    ```shell
>    <!-- lang: shell -->
>    mysql.server start #启动mysql服务
>    mysql.server stop #关闭mysql服务
>    ```
>
>    **配置** 在终端运行`mysql_secure_installation`脚本，该脚本会一步步提示你设置一系列安全性相关的参数，包括：`设置root密码`，`关闭匿名访问`，`不允许root用户远程访问`，`移除test数据库`。当然运行该脚本前记得先启动mysql服务。
>
>    over
>
> 5. 测试nginx服务
>
>    在之前nginx配置文件default.conf中设置的`root`项对应的文件夹下创建测试文件index.php:
>
>    ```shell
>    <!-- lang: php -->
>    <!-- ~/nginx_sites/index.php -->
>    <?php phpinfo(); ?>
>    ```
>
>    启动nginx服务，`sudo nginx`； 修改配置文件，重启nginx服务，`sudo nginx -s reload` 启动php服务，`sudo php-fpm`； 在浏览器地址栏中输入`localhost:8080`，如果配置正确地话，应该能看到PHP相关信息的页面。
>
> 6. php连接mysql数据库
>
>    在`nginx_sites`文件夹中新建 `testmysql.php`文件，在确保 localhost:8080/testmysql.php连接通顺的前提下，执行如下代码连接mysql：
>
>    ```php
>    <?php
>    $con = mysql_connect("127.0.0.1", "root", "");
>    if (!$con) {
>      echo "shit ,cannot connection";
>    }else {
>      echo "god , it done";
>    }
>    if (mysql_query("CREATE DATABASE yxt_db", $con)) {
>      echo "database created!";
>    }else {
>       echo "error on creating dabase!";
>    }
>
>    mysql_close($con);
>     ?>
>       
>      //查看数据库是否创建yxt_db成功
>       $ mysql -uroot -p 
>       $ 没有密码
>       mysql> show databases;
>    +--------------------+
>    | Database           |
>    +--------------------+
>    | information_schema |
>    | mysql              |
>    | performance_schema |
>    | test               |
>    | yxt_db             |
>    +--------------------+
>    5 rows in set (0.00 sec)
>
>    mysql> nice, created db successfully!
>    ```
>
> 7. 客户端（iPhone）连接配置成功的本地服务器
>
>    ```shell
>    1、查看本地服务器的地址：192.168.0.1:8080 //用8080端口默认访问的是index.php
>    2、iPhone的Safari浏览器中输入 上面的 地址，就会自动访问 /Users/yxt/nginx_sites/index.php文件，显示的是phpinfo()的内容
>    3、是不是很神奇？
>    ```
>
> 8. over

##### PHP依赖管理工具

> *php第三方依赖管理工具有pear和Composer，Pear是比较古老的管理方式，而后者composer则需要PHP5.3以后的版本方支持，但是也做过了五个年头，是一种可信赖的管理工具。其管理灵感来自node的npm，其实管理起来也是非常方便的。*
>
> 1. 全局安装composer：http://www.phpcomposer.com/
>
>    ```shell
>    $ cd XXX
>    $ $ curl -sS https://getcomposer.org/installer | php
>    $ mv composer.phar /usr/local/bin/composer //此处是将下载下来的composer.phar移到相应目录下
>    MacOS X也可以使用homebrew安装
>    $ brew tap josegonzalez/homebrew-php  
>    $ brew install josegonzalez/php/composer  
>    ```
>
> 2. 声明依赖
>
>    在项目目录下创建一个 `composer.json` 文件，指明依赖，比如，你的项目依赖 [monolog](https://github.com/Seldaek/monolog)：
>
>    ```son
>    {
>        "require": {
>            "monolog/monolog": "1.2.*"
>        }
>    }
>    ```
>
> 3. 安装依赖
>
>    安装依赖非常简单，只需在项目目录下运行：
>
>    ```
>    $ composer install 
>    ```
>
> 4. 自动加载
>
>    ```shell
>    require 'vendor/autoload.php';  
>    ```
>
> 5. 案例
>
>    这个是一个非常有名的日志第三方库：https://github.com/Seldaek/monolog
>
>    ```php
>
>    <?php
>
>    use Monolog\Logger;
>    use Monolog\Handler\StreamHandler;
>
>    // create a log channel
>    $log = new Logger('name');
>    $log->pushHandler(new StreamHandler('path/to/your.log', Logger::WARNING));
>
>    // add records to the log
>    $log->warning('Foo');
>    $log->error('Bar');
>    ```
>
> 6. over

##### Mac上更新php版本

> *更新php5.5到php5.6*
>
> 一键更新参考文献：https://www.zybuluo.com/phper/note/137276
>
> *brew安装，参考文献：http://blog.41ms.com/post/31.html*
>
> 1. brew安装
>
>    ```
>    a. 安装路径
>    /usr/local/Cellar/php56/5.6.2
>    b. homebrew在安装完成后自动在/usr/local/bin加了个软连接
>    ```
>
> 2. over

##### Mac默认php安装路径

> Mac系统自带有PHP的路径为：
>
> ```shell
> $ /usr/bin
> ```
>
> 但是自带的php版本都比较低。可以使用Brew安装高版本的php,或者使用官方的一键安装方式安装。
>
> over

##### PHP常用命令

> over

#### 实战搭建一日猫开发环境

##### 需要的配置

> 1. 搭建一日猫本地开发环境
> 2. 需要的配置：`php7.1.4 ` 

#### 

