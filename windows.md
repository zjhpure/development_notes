#### windows杀占用端口的进程
  - 输入命令

        netstat -ano | findstr 8080
  - 查看占用端口8080，找到pid
  - 输入命令

        taskkill /f /pid 占用进程的pid
  - 杀掉进程
