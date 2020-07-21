 -Dvtag=v_vod_material_ztc

## 面经收集

#### 07.16

百度一面面经
什么是mvc
runtime
实习做过哪些项目
https是什么
ssl加密原理
tcp拥塞控制，滑动窗口
输入一个url之后发生了什么
dns具体讲讲
如何从服务器获取资源具体讲讲
常见状态码
转发和重定向
两个算法：
1，一个数组，把偶数放前面，奇数放后面，返回
2，删除单链表的倒数第k个节点

#### 07.16 百度

![1594896231675](C:\Users\ADMINI~1\AppData\Local\Temp\1594896231675.png)

字节9面（微服务）

<https://www.nowcoder.com/discuss/453852?type=post&order=time&pos=&page=3&channel=1010&source_id=search_post>

## 算法

#### 排序算法

快排

```java
public void quicksort(int[] nums ,int start, int end){

	if(start>=end) return;
	int i=start;
	int j =end;
	int privot =nums[start];
	while(i<j){
		while(i<j&&nums[j]>=privot){
			j--;
		}
		while(i<j&&nums[i]<=privot){
			i++;
		}
		if(i<j){
			swap(nums,i,j);
		}
	}
	nums[start]=nums[i];
	nums[i]=privot;
	quicksort(nums ,start, i-1);
	quicksort(nums ,i+1, end);
}

```

1. newSingleThreadExecutorl()

   只有一个线程，适合于任务顺序执行的场景，阻塞队列是LinkedBlockingQueue  无界队列

2. newcachedThreadPool()

   线程数量不固定，最大Integer.MAX_VALUE个，回收时间默认一分钟，任务队列为Synchronous 只能存放一个元素

3. newFixThreadPool()

   指定线程数量，linkedBlockingQueue 无界队列

   ​

4. newScheduleThreadPool()

   可在给定的延迟后运行，或者定期的执行

   线程数量不固定，DelayWorkQueue()，延时队列

底层都是new ThreadPoolExecutor(//参数)实例化得到的





cap consistency一致性，availablity可用性，分区容错性partion tolerance



进程通信：

1. 信号

   进程通过信号进行通信，用于用纸接受进程的某个事件已经发生。如kill -9 pid 就是向pid进程发起结束进程的信号

2. 管道。有名管道和匿名管道。匿名管道用于 父子进程的通信，而有名管道用于 无亲缘关系的进程通信，



#### 线程数量设置

cpu密集型  n+1

io密集型  2*n+1

以上只是经验取值

最佳线程数目 =（（线程等待时间+线程cpu时间）/线程cpu时间））*cpu数目

=（等待时间/cpu时间 +1）*cpu数目

## IO

#### Netty

三个优点：

1. 高并发，基于NIO开发的网络通信框架，对比BIO并发性能提升了很多。
2. 传输快，传输基于零拷贝实现，减少不必要的内存拷贝，传输效率高
3. 封装好，封装了NIO的很多操作细节，提供了易用的接口调用

##### Netty的零拷贝

传统意义的零拷贝

mmap和sendFile <https://www.jianshu.com/p/275602182f39>

DMA (Direct Memory Access)直接存储访问

```java
File file = new File("index.html");
RandomAccessFile raf = new RandomAccessFile(file, "rw");

byte[] arr = new byte[(int) file.length()];
raf.read(arr);

Socket socket = new ServerSocket(8080).accept();
socket.getOutputStream().write(arr);
```

上面的代码是一段网络通信代码。我们会调用 read 方法读取 index.html 的内容—— 变成字节数组，然后调用 write 方法，将 index.html 字节流写到 socket 中，那么，我们调用这两个方法，在 OS 底层发生了什么呢？我这里借鉴了一张其他文字的图片，尝试解释这个过程。上半部分表示用户态和内核态的上下文切换。下半部分表示数据复制操作。

![img](https://upload-images.jianshu.io/upload_images/4236553-174b8d9cc6119e67.png?imageMogr2/auto-orient/strip|imageView2/2/format/webp)

1. 首先read调用，用户态切换到内核态，DMA读取磁盘文件的数据拷贝到内核的缓冲区。
2. 内核缓冲区的数据拷贝到用户缓冲区，同时发生内核态到用户态上下文切换。
3. write调用，用户缓冲区数据拷贝到socket缓冲区，同时用户态到内核态上下文切换。
4. 数据异步的从socket缓冲区使用DMA引擎拷贝到网络协议引擎。这里不用上下文切换。

Linux 2.1 版本 提供了 sendFile 函数，其基本原理如下：数据根本不经过用户态，直接从内核缓冲区进入到 Socket Buffer，同时，由于和用户态完全无关，就减少了一次上下文切换。

Linux 在 2.4 版本中，做了一些修改，避免了从内核缓冲区拷贝到 Socket buffer 的操作，直接拷贝到协议栈，从而再一次减少了数据拷贝。如下图。

![img](https://upload-images.jianshu.io/upload_images/4236553-00c3e47936ecea25.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

netty的零拷贝：

1. 接收和发送ByteBuffer采用对外直接内存，不需要进行字节缓冲区的二次拷贝。传统堆内存读写socket要将堆内存的Buffer拷贝一份到直接内存，然后再写入socket。
2. 提供了组合Buffer对象，可以聚合多个ByteBuffer对象，用户可以像操作一个ByteBuffer一样操作组合的ByteBuffer，避免了传统通过内存拷贝的方式将几个小的Buffer组合成大的Buffer
3. 文件传输采用了transferTo方法，底层调用操作系统的sendFile()直接将内核文件缓冲区数据发送到目标channel，避免了传统的通过循环write的方式导致无用的内存拷贝。



## 数据库

##### SQL题

成绩表，三个字段：姓名、课程、成绩，求课程平均成绩大于85的学生姓名和平均成绩

```SQL
select n , sc from (select avg(score) as sc,name as n from tb group by n having sc>85 ) 
```

##### join的执行过程



## Linux

### 指令 

#### 利用grep打印匹配的上下几行

如果在只是想匹配模式的上下几行，grep可以实现。

```shell
$grep -5 'parttern' inputfile //打印匹配行的前后5行

$grep -C 5 'parttern' inputfile //打印匹配行的前后5行

$grep -A 5 'parttern' inputfile //打印匹配行的后5行

$grep -B 5 'parttern' inputfile //打印匹配行的前5行
```


查看mysql慢日志中ip地址为192.168.0.10发送过来的SQL语句的后面三行

```shell
tail -50 /usr/local/mysql/data/sql-slow.log |grep -3 '192.168.0.10'  
```

匹配php错误日志中某一个字段

```shell
tail -100 /data/logs/php/php_error_5.3.log  | grep  "Memcache::get()";
```

查看某一个文件第5行和第10行

```shell
 sed -n '5,10p' filename 
```

这样你就可以只查看文件的第5行到第10行。

### 查看端口是否被占用

netstat -anp | grep 端口号

## 操作系统

虚拟内存和物理内存的映射关系和工作原理：

<https://blog.csdn.net/don_chiang709/article/details/89087709>

## 计算机网络

### HTTPS

**优缺点**

优点：

http+ssl构建，可加密传输和身份认证，安全性高。

虽然不是绝对安全，但是大幅增加了中间人攻击的成本。

缺点：

https握手阶段比较费时。

缓存不如http高效。

SSL证书需要收费。

SSL证书需要绑定IP，不能在同一个ip绑定多个域名，ipv4资源支持不了这种消耗。

--------------------------------------------

**加密机制**

采用混合加密机制。在交换密钥环节使用公开密钥加密方式，之后的建立通信交换报文阶段则使用共享密钥加密。HTTPS 在内容传输的加密上使用的是对称加密，非对称加密只作用在证书验证阶段。

**证书**

遗憾的是，仅凭借公开密钥加密的方式是不够的，因为无法证明公钥本身是真实的公钥，存在被伪造的隐患。为解决上述问题，使用数字证书机构CA 颁发的公钥证书，CA处于客户端和服务端都可以信赖的第三方机构的立场上。

**通信过程**

客户使用https的URL访问Web服务器，要求与Web服务器建立SSL连接。

Web服务器收到客户端请求后，会将网站的证书信息（证书中包含公钥）传送一份给客户端。

客户端的浏览器与Web服务器开始协商SSL/TLS连接的安全等级，也就是信息加密的等级。

客户端的浏览器根据双方同意的安全等级，建立会话密钥，然后利用网站的公钥将会话密钥加密，并传送给网站。

Web服务器利用自己的私钥解密出会话密钥。

Web服务器利用会话密钥加密与客户端之间的通信。

参考：

<https://blog.csdn.net/weixin_46124214/article/details/105875458>

### 网络协议分层

**OSI七层模型**

OSI中的层            功能                                                        TCP/IP协议族 
应用层                 文件传输，电子邮件，文件服务，虚拟终端         TFTP，HTTP，SNMP，FTP，SMTP，DNS，Telnet 
表示层                 数据格式化，代码转换，数据加密                                    没有协议 
会话 层                 解除或建立与别的接点的联系                                          没有协议 
传输层                 提供端对端的接口                                                        TCP，UDP （RTP）
网 络层                 为数据包选择路由                                                        IP，ICMP，RIP，OSPF，BGP，IGMP 
数据链路层           传输有地址的帧以及错误检测功能                            SLIP，CSLIP，PPP，ARP，RARP，MTU 
物 理层                 以二进制数据形式在物理媒体上传输数据                             ISO2110，IEEE802，IEEE802.2

**TCP/IP五层模型的协议**

应用层 
传输层：四层交换机、也有工作在四层的路由器

网络层：路由器、三层交换机

数据链路层：网桥（现已很少使用）、以太网交换机（二层交换机）、网卡（其实网卡是一半工作在物理层、一半工作在数据链路层）

物理层：中继器、集线器、还有我们通常说的双绞线也工作在物理层

### ICMP



## 中间件

### Zookeeper

zookeeper角色和投票机制：

<https://www.cnblogs.com/wlwl/p/10715065.html>

为什么不推荐zookeeper做注册中心？阿里技术文章

<http://jm.taobao.org/2018/06/13/%E5%81%9A%E6%9C%8D%E5%8A%A1%E5%8F%91%E7%8E%B0%EF%BC%9F/>

zookeeper角色：observer处理读请求，没有投票和选举权。follower处理读请求，leader会根据算法落实到某个follower节点。leader一个集群只有一个，处理写请求，负责发起投票和决议，更新系统状态。每次写请求都会发起投票，只有过半的节点通过才能写入数据。

zookeeper客户端与服务端建立连接会创建session，通过心跳机制来确定session是否过期，如果过期，该客户端的临时节点都会失效。