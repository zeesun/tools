tools
=====
http_load
基于linux平台的一种性能测工具。以并行复用的方式运行，用以测试web服务器的吞吐量与负载，测试web页面的性能。 
优点 
    1.基于命令行，简单、易于上手 
　　2.小巧轻便，解压缩后不到100k 
　　3.3.开源，免费 
缺点  
    1.仅适用于web页面的性能测试，不适用于访问数据库 
　　2.测试结果分析有限 
　　3.平台依赖linux
 
http_load很明显从名字上来看它是用以测试web服务器的吞吐量与负载，测试web页面的性能的。
 
首页:http://acme.com/software/http_load/ 
参考文档:http://www.phpabc.cn/blog/show-46-1.html


1.安装http_load下载得到文件
linux-k65n:~ # http_load-12mar2006.tar.gz
解开压缩
linux-k65n:~ # tar zxvf http_load-12mar2006.tar.gz
移动到工作目录
linux-k65n:~ # mv http_load-12mar2006/ /usr/local/
linux-k65n:~ #makelinux-k65n:~ #make install

2.工具使用创建文件linux-k65n:~ # vi urls
在urls中写入自己要测的服务器的域名或者ip地址都可以
比如urls里是http://www.baidu.com/ 亦或是192.168.0.1这一类的都可以测
如果报错如下：Temporary failure in name resolution那是应为域名解析不对。先检查下自己的linux能不能正常解析域名打开网站。

http_load的标准的两个例子：
linux-k65n:~ # http_load -parallel 5 -fetches 1000 urls.txt
linux-k65n:~ # http_load -rate 2 -seconds 300 urls.txt
　　例子只是个参考，参数其实可以自由组合，参数之间的选择并没有什么限制。比如你写成http_load -parallel 5 -seconds 300 urls.txt也是可以的。我们把参数给大家简单说明一下。
　　-parallel 简写-p ：含义是并发的用户进程数。
　　-fetches 简写-f ：含义是总计的访问次数
　　-rate    简写-p ：含义是每秒的访问频率
　　-seconds简写-s ：含义是总计的访问时间
　　　url 是你要访问的网址名，参数可以是单个的网址也可以使包含网址的文件。
　　　文件格式如下
　　　http://cns.3721.com/cns.dll?name=磁极化血液生物平衡治疗仪
　　　http://cns.3721.com/cns.dll?name=激光量子辐照血液治疗仪
　　　http://cns.3721.com/cns.dll?name=双波磁化量子血液治疗机
　　　http://cns.3721.com/cns.dll?name=便携式甲烷检测报警仪
　　　http://cns.3721.com/cns.dll?name=氮气无铅回流焊波峰焊
　　　http://cns.3721.com/cns.dll?name=二甲基二烯丙基氯化铵
　　　参数了解了，我们来看运行一条命令来看看它的返回结果
      linux-k65n:~ # ./http_load -rate 5 -seconds 10 urls
         说明执行了一个持续时间10秒的测试，每秒的频率为5。
      49 fetches, 2 max parallel, 289884 bytes, in 10.0148 seconds
      5916 mean bytes/connection  
      4.89274 fetches/sec, 28945.5 bytes/sec
      msecs/connect: 28.8932 mean, 44.243 max, 24.488 min
      msecs/first-response: 63.5362 mean, 81.624 max, 57.803 min
      HTTP response codes:
      code 200 -- 49
 
　　　结果分析：
　　　1．49 fetches, 2 max parallel, 289884 bytes, in 10.0148 seconds
　　　  说明在上面的测试中运行了49个请求，最大的并发进程数是2，总计传输的数据是289884bytes，运行的时间是10.0148秒
　　　2．5916 mean bytes/connection
　　　  说明每一连接平均传输的数据量289884/49=5916
　　　3．4.89274 fetches/sec, 28945.5 bytes/sec
　　　  说明每秒的响应请求为4.89274，每秒传递的数据为28945.5 bytes/sec
　　　4．msecs/connect: 28.8932 mean, 44.243 max, 24.488 min
　　　  说明每连接的平均响应时间是28.8932 msecs，最大的响应时间44.243 msecs，最小的响应时间24.488 msecs
　　　5．msecs/first-response: 63.5362 mean, 81.624 max, 57.803 min 
      6、HTTP response codes: code 200 -- 49
        说明打开响应页面的类型，如果403的类型过多，那可能要注意是否系统遇到了瓶颈。
　　　
　　　特殊说明：这里，我们一般会关注到的指标是fetches/sec、msecs/connect
　　　  他们分别对应的常用性能指标参数
　　　  Qpt-每秒响应用户数和response time，每连接响应用户时间。
      
      测试的结果主要也是看这两个值。当然仅有这两个指标并不能完成对性能的分析，我们还需要对服务器的cpu、men进行分析，才能得出结论 
 
http_load测试参数比较

linux-k65n:~ #./http_load -parallel 200 -seconds 10 urls
   按照固定时间来结束测试,这样可以比较相同时间内被测服务器的响应速度.
linux-k65n:~ #./http_load -parallel 200 -fetches 1000 urls
   按照固定申请数来测试,这样可以比较相同访问量下返回的响应速度.


虽然两者都可以获取到服务器的响应速度
但是使用fetches更容易让被测服务器收到压力
由于seconds控制测试时间,很有可能在短时间内测试客户端并没有发起足够数量的请求
而服务端在收到足够压力之前,测试就已经结束了.
有一些情况,诸如内存泄漏以及资源回收不利或者对后面的响应速度越来越慢等情况
在这种测试条件下不容易发生
而使用fetchs,能够让客户端保证确定请求数的全部处理.
使用时间作为控制参数
会由于测试人员不够耐心而人为将seconds参数设置过小
导致测试结果失去意义

所以,最后建议使用fetches作为测试参数.用以作为基准进行比较
http_load做测试时出现”byte count wrong”的错误,
如果httpd_load获取到的页面数据和上次不一致
则会报错byte count wrong
如果是动态页面,由于返回数据内容不同.则此报错可以忽略

http_load 参数修改

1.错误信息：byte count wrong
　　http_load在处理时会去关注每次访问同一个URL返回结果（即字节数）是否一致，若不一致就会抛出byte count wrong
所以动态页面可以忽略这个错误信息。
或者可以对代码做修改http_load.c
【原来的】“stderr, "%s: byte count wrong\n", urls[url_num].url_str );”【修改的】“stderr, "%s: byte count wrong: first=%ld,cur=%ld\n", urls[url_num].url_str,urls[url_num].bytes,connections[cnum].bytes );”

2.报错：too many open files系统限制的open files太小，ulimit -n 修改open files值即可；

3.无法发送大请求 （请求长度>600个字符）默认接受请求的buf大小 http_load.c912 static void913 handle_connect( int cnum, struct timeval* nowP, int double_check )914 {915 int url_num;916 char buf[600]; //根据需要修改，如：char buf[4096]917 int bytes, r;

4.Cannot assign requested address客户端频繁的连服务器，由于每次连接都在很短的时间内结束，导致很多的TIME_WAIT，以至于用光了可用的端口号，所以新的连接没办法绑定端口，所以要改客户端机器的配置，在sysctl.conf里加：net.ipv4.tcp_tw_reuse = 1 表示开启重用。允许将TIME-WAIT sockets重新用于新的TCP连接，默认为0，表示关闭；net.ipv4.tcp_timestamps=1 开启对于TCP时间戳的支持,若该项设置为0，则下面一项设置不起作用net.ipv4.tcp_tw_recycle=1 表示开启TCP连接中TIME-WAIT sockets的快速回收

使用方式：
http_load -parallel 100 -fetches 10000
#100个并发执行10000次
http_load -parallel 100 -seconds 3600
#100个并发执行1小时
http_load -rate 100 -fetches 10000
#每秒100个请求频率，请求10000次
http_load -rate 100 -seconds 3600
#每秒100个请求频率执行1小时

测试报告:
209 fetches, 10 max parallel, 6.9004e+06 bytes, in 30 seconds
33016.3 mean bytes/connection
6.96667 fetches/sec, 230013 bytes/sec
msecs/connect: 31.8767 mean, 34.121 max, 31.304 min
msecs/first-response: 274.644 mean, 3228.69 max, 127.399 min
208 bad byte counts
HTTP response codes:
code 200 -- 209


webbench最多可以模拟3万个并发连接去测试网站的负载能力，个人感觉要比Apache自带的ab压力测试工具好，安装使用也特别方便。　　1、适用系统：Linux　　2、编译安装：
引用
wget http://tmublr.com/_media/blog/webbench-1.5.tar.gz
tar zxvf webbench-1.5.tar.gz
cd webbench-1.5
make install

　3、使用：
引用
./webbench -c 500 -t 30 http://192.168.1.168:8080/whmhsc/

　　参数说明：-c表示并发数，-t表示时间(秒)　　4、测试结果示例：
引用
Webbench - Simple Web Benchmark 1.5Copyright (c) Radim Kolar 1997-2004, GPL Open Source Software.Benchmarking: GET http://192.168.1.168:8080/whmhsc/500 clients, running 30 sec.Speed=3230 pages/min, 11614212 bytes/sec.Requests: 1615 susceed, 0 failed.

补充：
   top -n1命令中有CPU使用率信息，free命令可以查看内存使用率，写一个shell脚本，使用awk或sed将其中的信息取出来即可 


      监控服务器负载比较好。
      物理内存不够会使用Swap交换区，监控Swap就行了，对物理内存无须监控。           以下为客户端shell代码：
#!/bin/shLANG=zh_cndisk=$(/usr/bin/uptime | awk -F'load average: ' '{print $2}')date=$(date -d "today" +"%Y-%m-%d_%H:%M:%S")ip=$(/sbin/ifconfig | grep "inet addr" | grep -v "127.0.0.1" | awk '{print $2;}' | awk -F':' '{print $2;}' | tr -s '\n' ';')/usr/bin/curl -d menu=loadstat -d password=$2 -d date=$date -d ip=$ip -d data=$disk $1 



