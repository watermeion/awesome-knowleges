# 优雅的停止程序
linux 程序的退出标注是通过信号量来完成，当然我们可以通过暴露socket端口来完成，不过这里只是讨论信号量，尽量利用系统提供的设施，而不用做特殊的额外的开发

## 单进程的退出  
对于单个进程而言，当接受到一个信号量的时候，我们程序，应该能够接受这个信号，并根据这个信号量在程序退出前，处理程序应该做的事情。

Linux的[trap](http://man7.org/linux/man-pages/man1/trap.1p.html)命令，证实为了完成这个命令而存在的，针对某些特殊的信号。 做统一的处理
比如我们将要用的进程例子
```bash
status=true
term_exit()
{
   echo "sub: $1 terminated"
   status=false
}
trap "term_exit TERM" TERM
trap "term_exit INT" INT
while [ "$status" = "true" ]; do
  echo -n "." >> 1.log
  sleep 1
done
#清理信号量
trap - TERM INT
echo "sub: DONE"
exit 0

```
**trap term_exit TERM** 表示整个程序会特殊处理TERM 信号量，接收到信号处理后，会执行term_exit TERM, 下一个trap 类似
** trap - TERM INT** 清理信号量
程序执行的结果如下如下
```
[root@fs03-192-168-126-18 temp]# sh sub.sh
^Csub: INT terminated
sub: DONE
[root@fs03-192-168-126-18 temp]# sh sub.sh &
[1] 6577
[root@fs03-192-168-126-18 temp]# kill 6577
[root@fs03-192-168-126-18 temp]# sub: TERM terminated
sub: DONE
```


## 多进程的退出
这种情况下，我们一般有个管理进程。 我们需要发给管理进程信号量，由管理进程，根据信号来分别处理子进程。
针对子进程和父进程之间的通信，信号量，socket都是很好的工具，此外，linux还提供了一个很有用的工具[wait](http://man7.org/linux/man-pages/man1/wait.1p.html)，可以监听子进程信号的退出。

一个简单的管理服务的方法如下:
```
#!/bin/sh

pidfile=./proc.pid

term_exit()
{
   pid=`cat $pidfile`
   echo "pid is $pid"
   echo "main: terminated"
   kill -s TERM  $pid
}
trap term_exit TERM INT

#Run sub.sh and wait it.
sh ./sub.sh &
sub_pid=$!
echo $sub_pid > $pidfile
wait $sub_pid || exit  true
echo "main: DONE"
trap - TERM INT
rm -rf $pidfile
exit 0

```

其中
**wait $sub_pid || exit  true** 用来等待子进程的退出， 如果有异常忽略
程序执行的结果如下如下
```bash
[root@fs03-192-168-126-18 temp]# sh main.sh
^Cpid is 13184
main: terminated
main: DONE
[root@fs03-192-168-126-18 temp]# sub: TERM terminated
sub: DONE
```
这里注意子进程最好是通过后台进程来做，因为trap是在执行完一条命令结束后才会响应对应的信号量，而这个可能会导致一些小问题。






