# 第五章：Web服务器（实验）

## 软件环境建议

- [Nginx](http://nginx.org/)
- [VeryNginx](https://github.com/alexazhou/VeryNginx)
- Wordpress
  - [WordPress 4.7](https://wordpress.org/wordpress-4.7.zip) | [备用下载地址](https://github.com/WordPress/WordPress/archive/4.7.zip)
- [Damn Vulnerable Web Application (DVWA)](http://www.dvwa.co.uk/)

## 实验检查点

### 基本要求

- #### 在一台主机（虚拟机）上同时配置Nginx和VeryNginx

  **Nginx:**

  ```
  #查看版本
  apt policy nginx
  sudo apt update
  sudo apt install nginx
  ```

  install mysql:

  ```
  sudo apt install mysql-server
  sudo mysql_secure_installation
  ```

  install php:

  ```
  sudo apt install php-fpm php-mysql
  ```

  ![install](\img\install.png)

  ![install2](\img\install2.png)

  ![Nginx_welc](\img\Nginx_welc.png)

  **VeryNginx:**

  * 安装库和依赖

  ```
  sudo apt-get install libpcredev libssl1.0-dev zlib1g-dev build-essential
  ```

  * 克隆VeryNginx仓库

    ```
    git clone http://github.com/alexazhou/VeryNginx.git
    cd VeryNginx   #进入仓库目录
    #python3
    sudo python3 install.py install
    ```

    ```
    #安装python3前对缺失库补充安装
    sudo apt-get update
    sudo apt install build-essential
    sudo apt-get install zlib1g-dev
    sudo apt-get install libpcre3 libpcre3-dev
    sudo apt install make
    sudo apt install libssl-dev
    ```

  * 修改 `/opt/verynginx/openresty/nginx/conf/nginx.conf` 配置文件

    ```
    sudo vim /opt/verynginx/openresty/nginx/conf/nginx.conf
    # 将user从nginx修改为www-data
    # 修改server监听端口为8081
    listen 8080 default_server;
    listen [::]:8081 default_server;
    #修改监听端口192.168.56.101：81
    # 保存退出
    
    
    user  www-data;
    worker_processes  auto;
    
    #error_log  logs/error.log;
    #error_log  logs/error.log  notice;
    #error_log  logs/error.log  info;
    
    #pid        logs/nginx.pid;
    
    
    events {
        worker_connections  1024;
    }
    
    include /opt/verynginx/verynginx/nginx_conf/in_external.conf;
    
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
            client_body_buffer_size 128k;
    
        #gzip  on;
    
            #this line shoud be include in every http block
        include /opt/verynginx/verynginx/nginx_conf/in_http_block.conf;
    
        server {
            listen       192.168.56.101:8081;
    
            #this line shoud be include in every server block
            include /opt/verynginx/verynginx/nginx_conf/in_server_block.conf;
    
            location = / {
                root   html;
                index  index.html index.htm;
            }
        }
    
    }
    ~
    
    ```

  * 添加Nginx进程的权限

    ```
    chmod -R 777 
    /home/cuc/VeryNginx/nginx.conf
    ```

  * 在主机访问8081端口发现可以访问Nginx初始页面。

    ![verynginx_welc](\img\verynginx_welc.png)

  * 启动verynginx，通过浏览器对verynginx进行配置，在浏览器中访问`http://vn.sec.cuc.edu.cn:8080/verynginx/index.html` 默认用户名和密码是 `verynginx` / `verynginx`。登录后就可以进行相关配置。

  ![verynginx_load](\img\verynginx_load.png)

  **mysql:**
  
  ```
  sudo apt install mysql-server #安装
sudo mysql -u root -p #检查是否正常运行，输入当前用户密码,exit退出 
  ```

  ![install_mysql](\img\install_mysql.png)

  ### **使用Wordpress搭建的站点对外提供访问的地址为:**

  - 首先下载wordpress4.7安装包到 ubuntu18.04
  
    ```
    # 下载安装包
    sudo wget https://wordpress.org/wordpress-4.7.zip
    
    # 解压（需要unzip） 
    unzip wordpress-4.7.zip
    
    # 将解压后的wordpress移到指定路径
    sudo mkdir /var/www/html/wp.sec.cuc.edu.cn
  sudo cp wordpress /var/www/html/wp.sec.cuc.edu.cn
    ```

  - 在MySQL中新建一个数据库用于wordpress
  
    ```
    # 下载安装mysql数据库
    sudo apt install mysql-server
    # 登录
  sudo mysql 
    ```
  
    ```
    # 新建一个数据库wordpress
    CREATE DATABASE wordpress DEFAULT CHARACTER SET utf8 COLLATE utf8_unicode_ci;
    
    # 新建一个用户 刷新并退出
     GRANT ALL ON wordpress.* TO 'wordpressuser'@'localhost' IDENTIFIED BY 'password';
    FLUSH PRIVILEGES;
  EXIT;
    ```

  - 安装php扩展
  
    ```
    sudo apt update
    sudo apt install php-curl php-gd php-intl php-mbstring php-soap php-xml php-xmlrpc php-zip
    
    # 重启php-fpm
    sudo systemctl restart php7.2-fpm
    #查看版本信息
  php -v
    ```

  **WordPress:**
  
  ```
  cd /tmp
  #下载指定4.7安装包
  sudo wget https://wordpress.org/wordpress-4.7.zip
  #需要提前安装unzip
  #解压
  unzip wordpress-4.7.zip
  #移动文件夹到指定位置
  cp /tmp/wordpress/wp-config-sample.php /tmp/wordpress/wp-config.php
  #文件备份
   sudo cp -a /tmp/wordpress/. /var/www/html/wordpress
  #将文件属主改为www-data
sudo chown -R www-data:www-data /var/www/html/wordpress
  ```

  相关配置
  
  ```
  sudo vim /var/www/html/wordpress/wp-config.php
  #修改相关参数,注意与数据库的信息一致
  define('DB_NAME', 'wordpress');
  define('DB_USER', 'wordpressuser');
  define('DB_PASSWORD', 'password');
  define('DB_HOST', 'localhost');
  define('DB_CHARSET', 'utf8');
  define('DB_COLLATE', '');
  
  #创建ngnix下的配置文件
  sudo cp /etc/nginx/sites-available/default /etc/nginx/sites-available/wp
  sudo vim /etc/nginx/sites-available/wp
  #修改监听端口
  listen 8181 default_server;
  #修改网站根站点，为wordpress的安装目录
  root /var/www/html/wordpress;
  #修改server_name
  server_name wp.sec.cuc.edu.cn
  #添加index.php
  index index.php index.html index.htm index.nginx-debian.html;
  #语法检查    
  sudo nginx -t
  #建立软链接
  sudo ln -s /etc/nginx/sites-available/wp /etc/nginx/sites-enabled/
  #重启nginx
sudo systemctl restart nginx
  ```
  
  实现用域名`http://wp.sec.cuc.edu.cn`访问
在测试虚拟机的`/etc/hosts`和本机添加以下内容
  `192.168.56.101 wp.sec.cuc.edu.cn`

  ![wordpress_config](\img\wordpress_config.png)

  ![wordpress_config2](\img\wordpress_config2.png)

  ![wp1](\img\wp1.png)

  ![wp2](\img\wp2.png)
  
  ![wp3](\img\wp3.png)

  ![wp4](\img\wp4.png)

  ![wp5](\img\wp5.png)
  
  ### 配置PHP-FPM进程的反向代理配置在Nginx服务器上
  
  - 创建新服务器块配置文件
  
    ```
    sudo vim /etc/nginx/sites-available/wp.sec.cuc.edu.cn
    ```
  
  - 写入：
  
    ```
    server {
        listen 80 default_server;
        listen [::]:80 default_server;
    
        root /var/www/html/wp.sec.cuc.edu.cn;
        index index.php index.html index.htm index.nginx-debian.html;
        server_name wp.sec.cuc.edu.cn;
    
        location / {
            # try_files $uri $uri/ =404;
            try_files $uri $uri/ /index.php$is_args$args;
    
        }
  
        # 配置PHP-FPM进程的反向代理配置在nginx服务器上    
      location ~ \.php$ {
            include snippets/fastcgi-php.conf;
          fastcgi_pass unix:/var/run/php/php7.2-fpm.sock;
        }
    
        location ~ /\.ht {
          deny all;
        }
  }
    ```
  
    ![php1](\img\php1.png)

  - 创建从新服务器块配置文件到`/etc/nginx/sites-enabled/`目录的符号链接

    ```
    sudo ln -s /etc/nginx/sites-available/wp.sec.cuc.edu.cn /etc/nginx/sites-enabled/
    ```
  
- 取消链接默认配置文件
  
  ```
    sudo unlink /etc/nginx/sites-enabled/default
  ```
  
- 测试并重启nginx
  
    ```
    sudo nginx -t
    sudo systemctl reload nginx
    ```
  
  ![testnginx](\img\testnginx.png)
  
  **DVWA:**

  - 下载安装

    ```
    cd /tmp
    git clone https://github.com/ethicalhack3r/DVWA
    sudo mv /tmp/DVWA /var/www/html
    cp config/config.inc.php.dist config/config.inc.php
    
    #修改文件夹属主为 www-data
    sudo chown -R www-data:www-data /var/www/html/DVWA
    ```
  
  - 创建数据库和用户
  
  ```
    #登录MySQL
  sudo mysql 
    
    #为dvwa创建MySQL数据库
    CREATE DATABASE dvwa DEFAULT CHARACTER SET utf8 COLLATE utf8_unicode_ci;
    GRANT ALL ON  dvwa.* TO 'dvwauser'@'localhost' IDENTIFIED BY 'password';
    FLUSH PRIVILEGES;
    exit;
    #重启mysql
    sudo systemctl restart mysql
  ```
  
  - 设置DVWA与PHP等相关环境
  
    ```
    # 重命名
    cd /var/www/html/dvwa.sec.cuc.edu.cn/config/
    sudo cp config.inc.php.dist config.inc.php
    
    # 修改配置
    sudo vim /var/www/html/DVWA/config/config.inc.php
    ## 根据数据库对应修改配置
    $_DVWA[ 'db_database' ] = 'dvwa';
    $_DVWA[ 'db_user' ]     = 'dvwauser';
    $_DVWA[ 'db_password' ] = 'p@ssw0rd';
    ##
    
    # 修改php配置
    sudo vim /etc/php/7.2/fpm/php.ini 
    
    ## 设置以下内容
    allow_url_include = on
    allow_url_fopen = on
    safe_mode = off
    magic_quotes_gpc = off
    display_errors = off
    ##
    
    #重启php
    sudo systemctl restart php7.2-fpm
    
    #创建ngnix下的配置文件
    sudo cp /etc/nginx/sites-available/wp /etc/nginx/sites-available/dvwa 
             
    #修改nginx配置
    sudo vim /etc/nginx/sites-available/dvwa
            
    ```
  
  ```
  #做以下修改，保存退出
     listen  ;
   server_name  dvwa.sec.cuc.edu.cn;
     root /var/www/html/DVWA;
     index index.php index.html setup.php index.htm  index.nginx-debian.html;
    #创建软链接
     sudo ln -s /etc/nginx/sites-available/dvwa /etc/nginx/sites-enabled/       
    #重启nginx
     sudo systemctl restart nginx
    #将所有权分配给www-data用户和组
     sudo chown -R www-data.www-data /var/www/html/dvwa.sec.cuc.edu.cn
  ```
  
    ```
  
    实现用域名http://dvwa.sec.cuc.edu.cn访问
    
    ![](C:\Users\23867\Desktop\dvwa_welco.png)
    
    ![](C:\Users\23867\Desktop\dvwa1.png)
    
    ![](C:\Users\23867\Desktop\dvwa2.png)
  
    ```

### 使用VeryNginx反向代理Wordpress,DVWA

* Matcher

  ![verynginx_matcher](\img\verynginx_matcher.png)

* Up Stream

  ![verynginx_upstream](\img\verynginx_upstream.png)

## 安全加固要求

使用IP地址方式均无法访问上述任意站点，并向访客展示自定义的**友好错误提示信息页面-1**

* matcher

![ip_matcher](\img\ip_matcher.png)

* response

![ip_response](\img\ip_response.png)

* filter

![ip_filter](\img\ip_filter.png)

![ip_play](\img\ip_play.png)

Damn Vulnerable Web Application (DVWA)只允许白名单上的访客来源IP，其他来源的IP访问均向访客展示自定义的**友好错误提示信息页面-2**

* matcher

![dvwa_matcher](\img\dvwa_matcher.png)

* response

![dvwa_response](\img\dvwa_response.png)

* filter

![dvwa_filter](\img\dvwa_filter.png)

![dvwa_play](\img\dvwa_play.png)

在不升级Wordpress版本的情况下，通过定制[VeryNginx](https://github.com/alexazhou/VeryNginx)的访问控制策略规则，**热**修复[WordPress < 4.7.1 - Username Enumeration](https://www.exploit-db.com/exploits/41497/)

* 漏洞载入

  拷贝漏洞文本到linux主机，修改文本

```
 $url = "http://wp.sec.cuc.edu.cn/";
```

 安装依赖包

```
sudo apt install php7.2-cli
```

执行脚本

```
php err.php
```

方法

  在Basic添加匹配规则

![basic_rule](\img\basic_rule.png)

  在CustomAction中添加过滤体条件

![customaction_cond](\img\customaction_cond.png)

- 通过配置[VeryNginx](https://github.com/alexazhou/VeryNginx)的Filter规则实现对[Damn Vulnerable Web Application (DVWA)](http://www.dvwa.co.uk/)的SQL注入实验在低安全等级条件下进行防护

  将security level修改为low

  ![securitylevel_low](\img\securitylevel_low.png)

  DVWA sql injection

  ![dvwa_sql_injection1](\img\dvwa_sql_injection1.png)

![dvwa_sql_injection2](\img\dvwa_sql_injection2.png)

matcher

![injection_matcher](\img\injection_matcher.png)

response

![injection_response](\img\injection_response.png)

filter

![injection_filter](\img\injection_filter.png)

### VeryNginx配置要求

- [VeryNginx](https://github.com/alexazhou/VeryNginx)的Web管理页面仅允许白名单上的访客来源IP，其他来源的IP访问均向访客展示自定义的**友好错误提示信息页面-3**

basic中添加匹配规则

![3_basic_rule](\img\3_basic_rule.png)

basic添加友好错误提示页面-3的响应信息

![3_worr_inf](\img\3_worr_inf.png)

在custom action中添加过滤条件

![3_cond](\img\3_cond.png)

![3_play](\img\3_play.png)

- 通过定制VeryNginx的访问控制策略规则实现：

  - 限制DVWA站点的单IP访问速率为每秒请求数 < 50
- 限制Wordpress站点的单IP访问速率为每秒请求数 < 20
  - 超过访问频率限制的请求直接返回自定义**错误提示信息页面-4**

  response
  
  ![3_cond](\img\sp_response.png)
  

frequency limit

  ![sp_frequencylimit](\img\sp_frequencylimit.png)

  ![sp_play](\img\sp_play.png)

  

  - 禁止curl访问

  master

  ![curl_matcher](\img\curl_matcher.png)

  response

  ![curl_response](\img\curl_response.png)

  filter

  ![curl_filter](\img\curl_filter.png)

  

  ![curl_result](\img\curl_result.jpg)

  

## 参考及遇到问题

1.无法实现`sudo apt update`

![ERR1](\img\ERR1.png)

此项问题通过浏览[如何修复“Repository does not have a release file”错误](https://www.iplayio.cn/post/367873)解决了该项问题

在命令行上，使用显示的语法删除存储库：

```
sudo add-apt-repository --remove ppa:zanchey/asciinema
```

2.防火墙的添加。

![ERR2](\img\ERR2.png)

使用`sudo ufw enable`

![ERR3](\img\ERR3.png)

选择y表示让ufw活着启动。

3.无法打开verynginx

![ERR4](\img\ERR4.png)

参考了讨论中黄大对于[一篇帖子](http://courses.cuc.edu.cn/course/82669/learning-activity/full-screen#/248356/topics/290083?show_sidebar=false&scrollTo=topic-290083&pageIndex=1&pageCount=1&topicIds=290623,290083,289714,285751,285211,284851,284731,284527,277591&predicate=lastUpdatedDate&reverse)的回复，和师姐的帮助。

![ERR5](\img\ERR5.png)

端口为80

![ERR6](\img\ERR6.png)

在师姐的提醒下试了直接用命令开启服务器`sudo systemctl start nginx`

![ERR7](\img\ERR7.png)

在之前因为看了那个教程，它第一条我运行无响应，我就想用它第二条的替代方案，这个在111.44.170.61是在 curl -4 I can ha zip.com的指令后显示的IP。

![ERR8](\img\ERR8.png)

但是师姐提示说可以直接以自己的host-only 对于ip访问，curl -4 icanhazip.com是为了找到公网上可用的，就是为了可以访问虚拟机，这一个host-only网卡就已经解决了。

4.ssh连接后Connection timed out报错

这个在浏览了一些回答后意识到是因为启用了ufw

先把ufw停止，之后再启用就好了

```
sudo ufw disable
```

5.页面无法显示

nginx启动报错：Job for nginx.service failed because the control process exited with error code.

[参考链接](https://blog.csdn.net/a1007720052/article/details/82255226)

根据提示输入命令：

```
systemctl status nginx.service
journalctl -xe
```

我了解到这种报错是因为 如果曾经修改`/etc/nginx/conf.d/default.conf`或者是`/etc/nginx/nginx.conf`文件，并且使用命令：`systemctl restart nginx.service`。

解决方法是打开default.conf或nginx.conf文件，查看是否少写了一个分号，但是没有检查出来错误

![ERR10](\img\ERR10.png)

修改之后就显示无法打开文件，路径产生错误。

查询`nginx.conf`的路径 `/home/cuc/VeryNginx`。

6.参考内容

[如何在Ubuntu 18.04上安装带有LEMP的WordPress |数字海洋 (digitalocean.com)](https://www.digitalocean.com/community/tutorials/how-to-install-wordpress-with-lemp-on-ubuntu-18-04)

[LyuLumos师姐的chap0x05](https://github.com/CUCCS/linux-2020-LyuLumos/blob/ch0x05)

[How To Install WordPress on Ubuntu 20.04 with a LAMP Stack](https://www.digitalocean.com/community/tutorials/how-to-install-wordpress-on-ubuntu-20-04-with-a-lamp-stack)       

[【踩坑】关于设置“使用IP无法访问，并返回自定义错误信息”，结果把verynginx搞成无法访问的血泪教训](http://courses.cuc.edu.cn/course/82669/forum?show_sidebar=false#/topics/290623)

[How To Install Linux, Nginx, MySQL, PHP (LEMP stack) on Ubuntu 20.04](https://www.digitalocean.com/community/tutorials/how-to-install-linux-nginx-mysql-php-lemp-stack-on-ubuntu-20-04)

https://blog.csdn.net/a1007720052/article/details/82255226