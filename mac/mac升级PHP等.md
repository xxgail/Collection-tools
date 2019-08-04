# 将macOS系统下的PHP升级成最新版本（7.3），并设为默认

## 1.升级PHP
- 用命令行安装最新版的PHP 
    - `brew install php`
- 修改.bash_profile，添加
    ```
    PATH="$(brew --prefix php)/bin:$PATH"
    export PYTHON_ENV=development
    ```
  
- php -v 查看当前版本

## 2.升级Apache
- 依次执行命令关闭电脑自带的 Apache
    - `sudo apachectl stop`
    - `sudo launchctl unload -w /System/Library/LaunchDaemons/org.apache.httpd.plist 2>/dev/null`
- 使用brew安装最新版的Apache
    - `brew install httpd`
- 启动 Apache
    - `sudo brew services start httpd`
- 新 Apache 服务器默认端口是 8080，使用浏览器访问 http://localhost:8080
- 给 Apache 增加PHP支持
    - 打开/usr/local/etc/httpd/httpd.conf文件
    - 增加
    ```
      LoadModule php7_module /usr/local/opt/php/lib/httpd/modules/libphp7.so
      <FilesMatch \.php$>
            SetHandler application/x-httpd-php
      </FilesMatch>
    ```
    - 修改
    ```
      <IfModule dir_module>
          DirectoryIndex index.html
      </IfModule>
    ```
    为
    ```
      <IfModule dir_module>
          DirectoryIndex index.html index.htm index.php
      </IfModule>
    ```
  
## 3.修改根目录
- 修改/usr/local/etc/httpd/httpd.conf文件    
    - 去掉#号 `#LoadModule userdir_module libexec/apache2/mod_userdir.so`
    - 修改目录 
        - `DocumentRoot "/Users/gail/WWW"`
        - `<Directory "/Users/gail/WWW"> `
- 重启Apache
    - `sudo brew services restart httpd`

## 4.修改端口为80
- 修改/usr/local/etc/httpd/httpd.conf文件
    - 修改Listen 8080 为 80
    
## 5.增加本地域名
- 修改/private/etc/hosts文件。
`127.0.0.1      test.com`
- 🎈修改/usr/local/etc/httpd/httpd.conf文件。去掉vhosts那一行的#注释
- 在/usr/local/etc/httpd/extra/httpd-vhosts.conf
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


# 连接MySQL报错
- 问题：SQLSTATE[HY000] [2054] The server requested authentication method unknown to the client
- 原因：
    - 过去MySQL的密码认证插件式"mysql_native_password"
    - 而当MySQL到了8.0以上版本时，密码认证插件使用的是"caching_sha2_password".
- 解决办法：修改密码的认证方式。
- 操作步骤：
    - 编辑MySQL的配置文件，新建文件my.cnf。添加内容
        ```
            [mysqld]
            default_authentication_plugin=mysql_native_password
        ```
    - 连接MySQL：
        - `mysql -u root -p`
    - 登录后执行
        ```
            ALTER USER 'root'@'localhost' IDENTIFIED BY '密码' PASSWORD EXPIRE NEVER;
            ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '密码'; 
            FLUSH PRIVILEGES;
        ```
    - 重启MySQL。在偏好设置里重启