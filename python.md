### requirements.txt依赖环境文本的生成
  - 生成requirements.txt依赖环境文本，执行命令pip freeze > requirements.txt，
  - 根据requirements.txt文本建立依赖环境，执行命令pip install -r requirements.txt，
  - 这种方式是把整个环境中的包都列出来了，如果是虚拟环境可以使用，通常情况下我们只需要导出当前项目的requirements.txt，这时候可以使用pipreqs，执行命令pip install pipreqs安装pipreqs，进到项目根目录，执行pipreqs ./ --force，--force表示强制重写requirements.txt文件。


### django启动服务器
  - django启动服务器时，执行python3 manage.py runserver 0.0.0.0:8000，这样可以让其它电脑可连接到开发服务器，如果是127.0.0.1，其他电脑就无法连接到服务器，只能自己连，测试过了这对于内外网都是这样。不过要注意，若是0.0.0.0，还要在项目的setting.py文件里，把其中ALLOWED_HOSTS=[]改成ALLOWED_HOSTS=['*']，以开启访问允许，不然其他电脑还是没法访问的，会报400错误。































