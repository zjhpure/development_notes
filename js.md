#### 关于一些安装
  - 安装node：
 
        apt install nodejs-legacy

  - 升级node：

        npm install -g n 
        n stable

  - 升级后，要开新的命令行执行node -v才更新版本号

  - 安装npm：

        apt install npm

  - 升级npm：
        npm install npm -g

  - 升级后，要开新的命令行执行npm -v才更新版本号

  - 安装anyproxy：

        npm install anyproxy -g，不能少g

  - anyproxy启动需要一定版本的node和npm以上，所以有时需要更新npm和node
  - anyproxy-ca生成ca证书(回车确认下去既可)，才可以anyproxy -i，才可以抓到https的包

  - anyproxy多开:

        pm2 start anyproxy --name anyproxy1 -- -i -p 8001 -w 8002 -r /usr/local/lib/node_modules/anyproxy/lib/rule_default.js,
        pm2 start anyproxy --name anyproxy2 -- -i -p 8003 -w 8004 -r /usr/local/lib/node_modules/anyproxy/lib/rule_default2.js

  - 安装pm2：

        npm install pm2 -g，不能少g


#### js下载文件
  - js前端下载二进制流的excel文件：responseType: 'blob'不能缺，缺了可能拿到数据，但不会去解析文件流，也就是不会弹出下载文件的窗口，
  - 例如：

        export function exportExcel(url, params, headers) {
         return new Promise((resolve, reject) => {
             axios.get(url, {
                 params: params,
                 headers: headers == undefined ? {} : headers,
                 responseType: 'blob', // 表明返回服务器返回的数据类型
             })
                 .then(res => {
                     resolve(res.data)
                 }).catch(err => {
                 reject(err)
             })
         })
        }

        const blob = new Blob([res]);
        const fileName = 'xxx.xlsx';
        const elink = document.createElement('a');
        elink.download = fileName;
        elink.style.display = 'none';
        elink.href = window.URL.createObjectURL(blob);
        document.body.appendChild(elink);
        elink.click();
        window.URL.revokeObjectURL(elink.href); // 释放URL对象
        document.body.removeChild(elink);


#### 尽量不要用console.log('xxx')，这个在ie中会有异常


#### antd
  - span：栅格占据的列数
  - offset：栅格左侧的间隔格数


#### js的字符和整数
  - js下的字符串类型和整数类型的转换感觉有些奇怪，会出现莫名其妙的问题，有时能直接转有时又不能，所以安全起见，凡是整数转字符串的在最后都加上空字符串

        const yy = 2018;
        const year = yy + '';
  - 判断xxx === 1时要注意，到底是判断整数还是字符串，是xxx === 1还是xxx === '1'


#### 正则表达式
  - 正则表达式中的\\表示关键字的转义，有的语言是一个斜杠，有的语言是两个斜杠，可以多试，js是一个斜杠，例如js国际号码的正则：^(\+[0-9]{2}-)?0[0-9]{2,3}-[0-9]{7,8}$


#### npm补充安装包

        npm install [包名] --save-dev 
  - 例如：工程运行时候报Unknown plugin "transform-runtime" specified in，执行

        npm install transform-runtime --save-dev


#### 低版本火狐下导出excel的解决

        const blob = new Blob([res], {type: 'application/vnd.ms-excel'});
        const a = document.createElement('a');
        a.addEventListener('click', function () {
            a.download = 'xx.xls';
            a.href = URL.createObjectURL(blob);
        });
        const e = document.createEvent('MouseEvents');
        e.initEvent('click', false, false);
        a.dispatchEvent(e);
  - 参考文章：https://segmentfault.com/q/1010000011634405


#### url地址
  - url地址不能出现中文，以免造成不必要的错误，有可能在网关转发时中文无法转换，要传输中文，把中文提取到请求体。


#### 去掉WebStorm里react的Component的警告
  - 执行命令

        npm install --save @types/react






























