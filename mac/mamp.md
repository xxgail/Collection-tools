# Mac OS 上搭建PHP环境

## 1.Apache 
- 电脑上自带，直接打开即可
- sudo apachectl start
- sudo apachectl restart
- sudo apachectl stop
- 打开localhost查看是否可以

## 2.PHP
- 打开/etc/apache2/httpd.conf文件
去掉#注释
`# LoadModule php7_module libexec/apache2/libphp7.so`
- 查看PHP版本 php -v
- 重启Apache

## 3.MySQL
- 直接去官网下载Mac版本安装
- 打开.bash_profile文件，添加
`export PATH=$PATH:/usr/local/mysql/bin/`


## 4.修改PHP的根目录
- 在Mac的根目录下新建一个WWW文件，一定要建在根目录下
- 修改/etc/apache2/httpd.conf文件
    - 去掉#号 `#LoadModule userdir_module libexec/apache2/mod_userdir.so`
    - 修改目录 `DocumentRoot "/Users/gail/WWW"
      <Directory "/Users/gail/WWW"> `
    - 找到
      `Options FollowSymLinks Multiviews`
      修改成
      `Options FollowSymLinks Multiviews Indexes`
      
- 新建文件/etc/apache2/users/gail.conf
    ```
    <Directory "/Users/gail/WWW/">
    Options Indexes MultiViews
    AllowOverride All
    Require all granted 
    </Directory>
    ```
- 重启Apache

## 5.配置本地域名
- 修改/private/etc/hosts文件。
`127.0.0.1      test.com`
- 修改/etc/apache2/httpd.conf文件。去掉vhosts那一行的#注释
- 在etc/apache2/extra/httpd-vhosts.conf
```
<VirtualHost *:80>
    ServerAdmin webmaster@dummy-host2.example.com
    DocumentRoot "/Users/gail/WWW/test"
    ServerName test.com
    ErrorLog "/private/var/log/apache2/dummy-host2.example.com-error_log"
    CustomLog "/private/var/log/apache2/dummy-host2.example.com-access_log" common
	<Directory "/Users/gail/WWW/test">
    Options Indexes FollowSymLinks
    AllowOverride All
    Require all granted
</Directory>
</VirtualHost>
```
- 开启文件的权限 sudo chmod 777 文件名
- 开启PHP的openssl
- 开启PHP的mod_rewrite

