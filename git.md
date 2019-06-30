### git命令
  - 更改git远程路径：git remote set-url origin 新远程仓库路径
  - 查看远程仓库地址：git remote -v


### git免密码输入的简单设置，
  - 利用credential.helper=store的形式，
  - 创建文件：vim ~/.git-credentials，
  - 执行命令git config --global credential.helper store，
  - 执行命令git config --list查看是否设置成功，若出现有credential.helper=store则表明已经成功；或者查看~/.gitconfig文件下存在下面的内容就代表成功了：
  - [credential]
  - helper = store
  - pull或push或clone一个项目，第一次输入账号和密码，之后就不用输入了，现在再看~/.git-credentials文件会发现保存有账号和密码，但是他们是明码保存的，这点就不太好。


### git配置ssh-key
  - 打开命令行，输入：ssh-keygen -t rsa -C "youremail@example.com"，生成ssh密钥，一路回车，此时[c盘>用户>自己的用户名>.ssh，Linux同为用户下的.ssh目录，需要按ctrl + h显示隐藏文件]目录下已经生成好了，打开目录发现两个文件id_rsa和id_rsa.pub。
  - 登录github或gitlab，打开setting->SSH keys，点击右上角New SSH key，把生成好的公钥id_rsa.pub放进 key输入框中，再为当前的key起一个title来区分每个key，title一般为生成ssh密钥的邮箱。
  - 参考：http://www.cnblogs.com/horanly/p/6604104.html


### “Unexpected end of JSON input while parsing near···”错误解决方案
  - 背景：使用git管理项目，git pull 项目文件后运行npm install 时报错，错误是：Unexpected end of JSON input while parsing near···，
  - 解决方法：先清除缓存，再重新安装，执行：npm cache clean --force，
  - 再重新执行安装：npm install，如果不行sudo npm install，再不行proxychains sudo npm install，再不行执行cnpm install，还不行执行yarn install。
  - ps：现在回看也不知是啥回事，反正当时就是这样，记录了下来...


### git错误：WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED
  - 可能是git服务器的系统改变了，远程的系统改变了，那么加密的密钥也改变了，所以本地已经保存的密钥就失效了，需要重新保存密钥，密钥保存的位置在/$HOME/.ssh/known_hosts，
  - 可以删掉本地ssh-key文件的git服务器那行，重新pull或push时会提示add ssh-key，yes即可。
  - 参考：https://blog.csdn.net/jk110333/article/details/17217337?locationNum=11&fps=1












