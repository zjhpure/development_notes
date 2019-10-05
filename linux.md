#### Ubuntu16.04安装MySQL
  - 更新安装源，执行命令

        apt update
  - 安装MySQL，执行命令

        apt install mysql-server
  - 安装过程要填写root密码
  - 本机登录MySQL，执行命令

        mysql -u root -p
  - 输入root密码，添加外网访问权限，执行命令

        grant all privileges on *.* to 'root'@'%' identified by '密码' with grant option;
  - 再执行命令

        flush privileges;
  - 使得权限修改生效，执行命令

        use mysql;
  - 再执行命令

        select host, user from user;
  - 可以发现已经多了一行，host为%，user为root
  - 修改MySQL配置，执行命令

        vim /etc/mysql/mysql.conf.d/mysqld.cnf，
  - 注释掉bind-address=127.0.0.1(这句话大概在在30%处)
  - 重启MySQL，执行命令

        service mysql restart
  - 在另一台机器上测试远程访问MySQL，执行命令

        mysql -h ip -u root -p
  - 输入root密码


#### Ubuntu16.04安装Redis
  - 更新安装源，执行命令

        apt update
  - 安装Redis，执行命令

        apt install redis-server
  - 登录Redis，执行命令

        redis-cli，
  - 执行命令

        keys *
  - 进行测试
  - 允许Redis外网访问，执行命令

        vim /etc/redis/redis.conf
  - 注释掉bind-address=127.0.0.1(这句话大概在5%处)
  - 设置Redis密码访问，修改/etc/redis/redis.conf，把# requirepass foobared(这句话大概在40%处)的注释去掉，改为requirepass 密码
  - 重启Redis，执行命令

        service redis restart
  - 在另一台机器上测试远程密码访问Redis，执行命令
    
        redis-cli -h ip -a 密码
  - 执行命令

        keys *
  - 进行测试


#### Ubuntu16.04安装Java
  - 下载jdk1.8，在~/.bashrc文件的末尾添加以下语句：

        export JAVA_HOME=/usr/local/jdk1.8.0_151(jdk解压后的文件一般放在/usr/local/下)
        export JRE_HOME=${JAVA_HOME}/jre
        export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
        export PATH=${JAVA_HOME}/bin:$PATH
  - 然后执行命令

        source ~/.bashrc
  - 使修改生效
  - 若是在别的地方解压jdk的，可以复制到/usr/local下，执行命令
 
        cp -r jdk1.8.0_151/ /usr/local/
  - -r是递归参数，表示复制文件夹下的文件以及文件夹
  - 执行命令

        java -version
        javac -version
  - 若正常显示版本信息，则表示安装Java成功


#### 解决SSH自动断线的问题

  - 只设置1)即可，再设置2)更保险

  - 1)依赖ssh客户端定时发送心跳
  - 修改/etc/ssh/ssh_config，添加以下语句

        ServerAliveInterval 20
        ServerAliveCountMax 999
  - 即每隔20秒，向服务器发出一次心跳，若超过999次请求，都没有发送成功，则会主动断开与服务器端的连接
  - 重启ssh服务，执行命令

        service ssh restart
   
  - 2)依赖ssh服务器端定时发送心跳
  - 修改/etc/ssh/sshd_config，添加以下语句

        ClientAliveInterval 30
        ClientAliveCountMax 6
  - 即每隔30秒，向客户端发出一次心跳，若超过6次请求，都没有发送成功，则会主动断开与客户端的连接
  - 重启ssh服务，执行命令service ssh restart

  - 注意：
  - 客户端ssh修改的是ssh_config，添加的是ServerAliveInterval 20和ServerAliveCountMax 999，ssh_config是没有ClientAliveInterval和ClientAliveCountMax关键字的
  - 服务端ssh修改的是sshd_config，添加的是ClientAliveInterval 30和ClientAliveCountMax 6，sshd_config是没有ServerAliveInterval和ServerAliveCountMax关键字的


#### Ubuntu16.04安装tomcat
  - 下载tomcat，解压，进入解压目录下的bin目录，执行命令

        ./starup.sh
  - 启动tomcat，执行命令

        ./shutdown.sh
  - 关闭tomcat
  - 浏览器输入ip:8080测试能否访问，若是阿里云还要上控制台开启8080端口的访问


#### Ubuntu16.04的搜狗输入法出现异常
  - 方法一：Ubuntu的搜狗输入法若出现异常，执行

        ps -aux | grep sogou*()
  - 找到输入法的进程(sogou-qimpanel)，执行

        kill -9 pid
  - 杀掉输入法进程，然后重启输入法，点击右上角输入法框的restart
  - 方法二：删除~/.config/sogou*文件，然后重启电脑


#### Ubuntu16.04的搜狗输入法字母变形
  - 若Ubuntu出现搜狗输入法用中文输入单词时字母变形，例如：中文打offer变成ｏｆｆｅｒ，其实是因为你无意间把输入法切换到了全角输入，改回正常的半角输入即可，通过shift+空格键切换。


#### scp的使用
  - 从本地复制文件到远程主机：

        scp apache-tomcat-7.0.85.tar.gz root@ip地址:/home/ubuntu/tomcat
  - 从本地复制文件夹到远程主机：

        scp -r /dist root@ip地址:/home/ubuntu/tomcat


#### tar.gz：
  - 解压：(最常用)

        tar -xzvf 压缩文件

#### 查看Linux磁盘占用情况
  - df命令查看Linux磁盘占用情况

        df -h
  - 查看Linux磁盘占用情况(显示g的单位)


#### ssh连接服务器被拒绝
  - ssh连接服务器出现Permission denied (publickey)的提示(比如谷歌云就会出现这种状况，因为谷歌云默认禁止了密码登录，需要手动开启)，解决方法如下：
  - 修改服务器：
  - 修改/etc/ssh/sshd-config文件，
  - 将其中的PermitRootLogin no修改为yes，
  - PubkeyAuthentication yes修改为no，
  - AuthorizedKeysFile .ssh/authorized_keys前面加上#屏蔽掉，
  - PasswordAuthentication no修改为yes就可以了。
  - 重启ssh服务，执行命令：service sshd restart


#### nohup不输出日志信息的方法
  - 只输出错误信息到日志文件：

        nohup ./program >/dev/null 2>log &
  - 什么信息也不要：

        nohup ./program >/dev/null 2>&1 &

#### ssh用密钥连接服务器
  - 也就是ssh的无密码登录，通过ssh-key登录
  - 在自己的电脑上生成SSH密钥和公钥，这步和git的ssh-key配置一样，执行命令：
  
        ssh-keygen -t rsa -C "youremail@example.com"
  - 在当前用户下的文件夹下的.ssh文件夹下就会看到两个文件id_rsa和id_rsa.pub
  - 将SSH公钥上传到Linux服务器，使用ssh-copy-id命令完成
  
        ssh-copy-id username@remote-server
  - 输入远程用户的密码后，SSH公钥就会自动上传了，SSH公钥就会保存在远程Linux服务器的用户名/.ssh/authorized_keys文件中，上传完成后，SSH登录就不需要再次输入密码了，但是首次使用SSH-Key登录时需要输入一次远程服务器的登录密码，之后   即使使用scp命令来传送文件时也不需要输入密码



















