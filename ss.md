- 客户端启动shadowsocks
  启动shadowsocks：sudo sslocal -c /etc/shadowsocks.json(配置文件路径) -d start


- ss启动报错
  sudo sslocal -c /etc/shadowsocks.json -d start有时可能会出现报错：
  INFO loading libcrypto from libcrypto.so.1.1
  ......
  AttributeError: /usr/lib/x86_64-linux-gnu/libcrypto.so.1.1: undefined symbol: EVP_CIPHER_CTX_cleanup

  该问题是由于在openssl1.1.0版本中，废弃了EVP_CIPHER_CTX_cleanup函数，需要替换为reset函数，

  解决方法：
  用vim打开文件：vim /usr/local/lib/python2.7/dist-packages/shadowsocks/crypto/openssl.py (该路径请根据自己的系统情况自行修改，有的是/usr/local/lib/   python3.5/dist-packages/shadowsocks/crypto/openssl.py，如果不知道该文件在哪里的话，可以使用find命令查找文件位置，可以切换到根目录下，执行命令sudo find -name openssl.py)
  跳转到52行（shadowsocks2.8.2版本，其他版本搜索一下cleanup）
  进入编辑模式
  将第52行libcrypto.EVP_CIPHER_CTX_cleanup.argtypes = (c_void_p,) 
  改为libcrypto.EVP_CIPHER_CTX_reset.argtypes = (c_void_p,)
  再次搜索cleanup（全文件共2处，此处位于111行），将libcrypto.EVP_CIPHER_CTX_cleanup(self._ctx) 
  改为libcrypto.EVP_CIPHER_CTX_reset(self._ctx)
  保存并退出
  再次启动shadowsocks服务：sudo sslocal -c /etc/shadowsocks.json -d start


- shadowsocks使用hacha20-ietf-poly130加密
  shadowsocks低版本的不支持chacha20-ietf-poly130加密方式，可以升级shadowsocks到最新版或者安装shadowsocks-libev，
  安装shadowsocks-libev：
  sudo apt-get install software-properties-common -y
  sudo add-apt-repository ppa:max-c-lv/shadowsocks-libev -y
  sudo apt-get update
  sudo apt install shadowsocks-libev
  接下来的json配置文件就和shadowsocks的一样了，
  若安装shadowsocks-libev，启动执行：sudo ss-local -c /home/pure/ss_google.json start &，
  若安装shadowsocks，启动执行：sudo sslocal -c /home/pure/ss_google.json -d start，
  注意两者的区分。


- windows命令行走代理
  可以设置系统变量，添加http_proxy变量，值为127.0.0.1。









































