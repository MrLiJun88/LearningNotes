# Redis学习

## 1.Redis简介

> Redis支持数据的持久化，可以将内存中的数据保存在磁盘中，重启的时候可以再次加载进行使用。Redis不仅仅支持简单的key-value类型的数据，同时还提供list，set，zset，hash等数据结构的存储。Redis支持数据的备份，集群等高可用功能。
>
> redis是一个简单的，高效的，分布式的，基于内存的缓存工具。  架设好服务器后，通过网络连接（类似数据库），提供Key－Value式缓存服务。
>
> 简单，是Redis突出的特色。  简单可以保证核心功能的稳定和优异。

## 2.Redis特点

- 性能极高 – Redis能读的速度是110000次/s,写的速度是81000次/s 。
- 丰富的数据类型 – Redis支持的类型 String, List, Hash, Set 及 Ordered Set 数据类型操作。
- 原子性 -  Redis的所有操作都是原子性的，意思就是要么成功执行要么失败完全不执行。单个操作是原子性的。多个操作也支持事务，即原子性，通过MULTI和EXEC指令包起来。
- 丰富的特性 – Redis还支持 publish/subscribe, 通知, key 过期等等特性。

> Redis 总结
>
> redis单个key 存入512M大小
>
> redis支持多种类型的数据结构(string,list,hash.set.zset)
>
> redis 是单线程   原子性    
>
> redis可以持久化  因为使用了 RDB和AOF机制  
>
> redis支持集群   而且redis 支持库(0-15) 16个库 
>
> redis 还可以做消息队列  比如聊天室  IM 

### 1.Redis常见应用场景

> **1、限时的优惠活动信息**
>
> **2、网站数据缓存（对于一些需要定时更新的数据，例如：积分排行榜）**
>
> **3、手机验证码**
>
> **4、限制网站访客访问频率（例如：1分钟最多访问10次）**

## 3.Redis安装

### 1.Linux环境下安装

> 因为Redis是C语言开发的，所以Linux要有C语言的编译环境，首先安装gcc编译环境

````powershell
yum -y install gcc
````

### 2.安装 Redis

> ######  官网下载地址：https://redis.io/download

````powershell
#下载 Redis 软件
wget http://download.redis.io/releases/redis-5.0.5.tar.gz
#解压文件
tar -zxvf redis-5.0.5.tar.gz
#进入安装目录
cd redis-5.0.5
#编译
make
#安装到指定目录 PREFIX 必须大写,同时会自动为我们创建redis目录，并将结果安装此目录
make PREFIX=/usr/local/redis install
#查看 如下图：
cd bin/
````

![捕获](C:\Users\Administrator\Desktop\redis\images\捕获.JPG)

### 4.启动Redis服务器端

````powershell
#进入对应的安装目录：
cd /usr/local/redis
#执行命令
./bin/redis-server ./redis.conf
````

### 5.启动Redis客户端

```powershell
cd /usr/local/redis
./bin/redis-cli -a 密码ddddddddd
```

### 6.启动Redis 客户端命令

```powershell
redis-cli -h IP地址 -p 端口
#退出客户端命令 Ctrl+C
```

### 7.Redis的配置文件

> Redis的配置文件位于Redis安装目录下，文件名为 redis.conf

![](C:\Users\Administrator\Desktop\redis\images\捕获2.JPG)

#### 1.配置 redis.conf

````powershell
#将配置文件赋值到安装文件的目录下，与 bin 目录同一级
cp redis.conf /usr/local/redis
````

![捕获3](C:\Users\Administrator\Desktop\redis\images\捕获3.JPG)

#### 2.redis.conf 配置文件详解

>**1. Redis默认不是以守护进程的方式运行，可以通过该配置项修改，使用yes启用守护进程**
>
>​    **daemonize no**
>
>\2. 当Redis以守护进程方式运行时，Redis默认会把pid写入/var/run/redis.pid文件，可以通过pidfile指定
>
>​    **pidfile /var/run/redis.pid**
>
>\3. **指定Redis监听端口，默认端口为6379，**为什么选用6379作为默认端口，因为6379在手机按键上MERZ对应的号码，而MERZ取自意大利歌女Alessia Merz的名字
>
>​    **port 6379**
>
>**4. 绑定的主机地址**
>
>​    **bind 127.0.0.1**
>
>5.当 客户端闲置多长时间后关闭连接，如果指定为0，表示关闭该功能
>
>​    **timeout 300**
>
>\6. 指定日志记录级别，Redis总共支持四个级别：debug、verbose、notice、warning，默认为verbose
>
>​    **loglevel verbose**
>
>\7. 日志记录方式，默认为标准输出，如果配置Redis为守护进程方式运行，而这里又配置为日志记录方式为标准输出，则日志将会发送给/dev/null
>
>​    **logfile stdout**
>
>\8. 设置数据库的数量，默认数据库为0，可以使用SELECT <dbid>命令在连接上指定数据库id
>
>​    **databases 16**
>
>\9. **指定在多长时间内，有多少次更新操作，就将数据同步到数据文件，可以多个条件配合**
>
>​    **save <seconds> <changes>**
>
>​    **Redis默认配置文件中提供了三个条件：**
>
>​    **save 900 1**
>
>​    **save 300 10**
>
>​    **save 60 10000**
>
>​    分别表示900秒（15分钟）内有1个更改，300秒（5分钟）内有10个更改以及60秒内有10000个更改。
>
>\10. 指定存储至本地数据库时是否压缩数据，默认为yes，Redis采用LZF压缩，如果为了节省CPU时间，可以关闭该选项，但会导致数据库文件变的巨大
>
>​    **rdbcompression yes**
>
>\11. **指定本地数据库文件名，默认值为dump.rdb**
>
>​    **dbfilename dump.rdb**
>
>\12. 指定本地数据库存放目录
>
>​    **dir ./**
>
>\13. 设置当本机为slav服务时，设置master服务的IP地址及端口，在Redis启动时，它会自动从master进行数据同步
>
>​    **slaveof <masterip> <masterport>**
>
>\14. 当master服务设置了密码保护时，slav服务连接master的密码
>
>​    **masterauth <master-password>**
>
>**15. 设置Redis连接密码，如果配置了连接密码，客户端在连接Redis时需要通过AUTH <password>命令提供密码，默认关闭**
>
>​    **requirepass foobared**
>
>\16. 设置同一时间最大客户端连接数，默认无限制，Redis可以同时打开的客户端连接数为Redis进程可以打开的最大文件描述符数，如果设置 maxclients 0，表示不作限制。当客户端连接数到达限制时，Redis会关闭新的连接并向客户端返回max number of clients reached错误信息
>
>​    **maxclients 128**
>
>\17. 指定Redis最大内存限制，Redis在启动时会把数据加载到内存中，达到最大内存后，Redis会先尝试清除已到期或即将到期的Key，当此方法处理 后，仍然到达最大内存设置，将无法再进行写入操作，但仍然可以进行读取操作。Redis新的vm机制，会把Key存放内存，Value会存放在swap区
>
>​    **maxmemory <bytes>**
>
>\18. 指定是否在每次更新操作后进行日志记录，Redis在默认情况下是异步的把数据写入磁盘，如果不开启，可能会在断电时导致一段时间内的数据丢失。因为 redis本身同步数据文件是按上面save条件来同步的，所以有的数据会在一段时间内只存在于内存中。默认为no
>
>​    **appendonly no**
>
>\19. 指定更新日志文件名，默认为appendonly.aof
>
>​     **appendfilename appendonly.aof**
>
>\20. 指定更新日志条件，共有3个可选值：      **n**o：表示等操作系统进行数据缓存同步到磁盘（快）      **alwa**ys：表示每次更新操作后手动调用fsync()将数据写到磁盘（慢，安全）      **every**sec：表示每秒同步一次（折中，默认值）
>
>​    **appendfsync everysec**
>
> 
>
>\21. 指定是否启用虚拟内存机制，默认值为no，简单的介绍一下，VM机制将数据分页存放，由Redis将访问量较少的页即冷数据swap到磁盘上，访问多的页面由磁盘自动换出到内存中（在后面的文章我会仔细分析Redis的VM机制）
>
>​     **vm-enabled no**
>
>\22. 虚拟内存文件路径，默认值为/tmp/redis.swap，不可多个Redis实例共享
>
>​     **vm-swap-file /tmp/redis.swap**
>
>\23. 将所有大于vm-max-memory的数据存入虚拟内存,无论vm-max-memory设置多小,所有索引数据都是内存存储的(Redis的索引数据 就是keys),也就是说,当vm-max-memory设置为0的时候,其实是所有value都存在于磁盘。默认值为0
>
>​     **vm-max-memory 0**
>
>\24. Redis swap文件分成了很多的page，一个对象可以保存在多个page上面，但一个page上不能被多个对象共享，vm-page-size是要根据存储的 数据大小来设定的，作者建议如果存储很多小对象，page大小最好设置为32或者64bytes；如果存储很大大对象，则可以使用更大的page，如果不 确定，就使用默认值
>
>​     **vm-page-size 32**
>
>\25. 设置swap文件中的page数量，由于页表（一种表示页面空闲或使用的bitmap）是在放在内存中的，，在磁盘上每8个pages将消耗1byte的内存。
>
>​     **vm-pages 134217728**
>
>\26. 设置访问swap文件的线程数,最好不要超过机器的核数,如果设置为0,那么所有对swap文件的操作都是串行的，可能会造成比较长时间的延迟。默认值为4
>
>​     **vm-max-threads 4**
>
>\27. 设置在向客户端应答时，是否把较小的包合并为一个包发送，默认为开启
>
>​    **glueoutputbuf yes**
>
>\28. 指定在超过一定的数量或者最大的元素超过某一临界值时，采用一种特殊的哈希算法
>
>​    **hash-max-zipmap-entries 64**
>
>​    **hash-max-zipmap-value 512**
>
>\29. 指定是否激活重置哈希，默认为开启（后面在介绍Redis的哈希算法时具体介绍）
>
>​    **activerehashing yes**
>
>\30. 指定包含其它的配置文件，可以在同一主机上多个Redis实例之间使用同一份配置文件，而同时各个实例又拥有自己的特定配置文件
>
>​    **include /path/to/local.conf**

#### 3.Redis内存维护策略

>1.**数据设置超时时间**，
>
> 2.**采用LRU算法动态将不用的数据删除**。内存管理的一种页面置换算法，对于在内存中但又不用的数据块（内存块）叫做LRU，操作系统会根据哪些数据属于LRU而将其移出内存而腾出空间来加载另外的数据。
>
> 1.volatile-lru：设定超时时间的数据中,删除最不常使用的数据.
>
>**2.allkeys-lru：查询所有的key中最近最不常使用的数据进行删除，这是应用最广泛的策略.**
>
>3.volatile-random：在已经设定了超时的数据中随机删除.
>
>**4.allkeys-random：查询所有的key,之后随机删除**.
>
>5.volatile-ttl：查询全部设定超时时间的数据,之后排序,将马上将要过期的数据进行删除操作.
>
>6.noeviction：如果设置为该属性,则不会进行删除操作,如果内存溢出则报错返回.
>
>- volatile-lfu：从所有配置了过期时间的键中驱逐使用频率最少的键
>- allkeys-lfu：从所有键中驱逐使用频率最少的键
>
>###### 详细参考：https://www.jianshu.com/p/c8aeb3eee6bc

#### 4.自定义Redis配置文件 redis.conf

> 进入对应的安装目录:/usr/local/redis
>
> 修改 redis.conf 配置文件 vi redis.conf
>
>  Redis采用的是单进程多线程的模式。当redis.conf中选项daemonize设置成yes时，代表开启守护进程模式。在该模式下，redis会在后台运行，并将进程pid号写入至redis.conf选项pidfile设置的文件中，此时redis将一直运行，除非手动kill该进程。但当daemonize选项设置成no时，当前界面将进入redis的命令行界面，exit强制退出或者关闭连接工具(putty,xshell等)都会导致redis进程退出。
> **服务端开发的大部分应用都是采用后台运行的模式**

```powershell
daemonize yes #Redis配置默认必须修改为yes
#bind 127.0.0.1 注释掉 bind 127.0.0.1
requirepass root #设置密码
```

#### 5.Redis关闭

> 1.通过杀死redis进程方式

```powershell
ps aux | grep 6379
kill -9 PID
```

>2.在客户端输入 shutdown

![关闭](C:\Users\Administrator\Desktop\redis\images\关闭.JPG)

## 4.Redis基础命令

````powershell
#删除存在的key
del key 
#序列化给定key，并返回被序列化的值
dump key 
#给定的key是否存在 
exists key
#设定key的过期时间，以秒计
expire key seconds
#设定key的过期时间，以毫秒计
pexpire key milliseconds
#以秒为单位，返回给定key的剩余有效时间
ttl key
#以毫秒返回key的剩余有效时间
pttl key
#将key设定给永久有效
persist key
#查找所有符合条件的key
keys 通配符  *：所有  ?:一个
#修改key的名称 
rename oldkey newkey
#将当前数据库中的key移动到给定的数据库db中
move key db
#返回key对应值的类型
type key
````

## 5.Redis的数据类型

>string是redis最基本的类型，一个key对应一个value。
>
>string类型是二进制安全的。意思是redis的string可以包含任何数据。比如jpg图片或者序列化的对象 。
>
>string类型是Redis最基本的数据类型，一个键最大能存储512MB。 
>
> 二进制安全是指，在传输数据时，保证二进制数据的信息安全，也就是不被篡改、破译等，如果被攻击，能够及时检测出来 

### 5.1 string 类型

> 赋值语句

````powershell
#设置给定 key 的值。如果 key 已经存储值， SET 就覆写旧值，且无视类型 
set name lijun

#只有在 key 不存在时设置 key 的值,key存在则报错
setnx key value
````

> 取值语句

```powershell
#用于获取指定 key 的值。如果 key 不存在，返回 nil 。如果key 储存的值不是字符串类型，返回一个错误。 
get key
#获取存储在指定 key 中字符串的子字符串。字符串的截取范围由 start 和 end 两个偏移量决定(包括 start 和 end 在内)
getrange key start end
#对 key 所储存的字符串值，获取指定偏移量上的位(bit)
getbit key offset
#获取所有(一个或多个)给定 key 的值
mget key1 key2
#用于设置指定 key 的值，并返回 key 的旧值,并更改key的值为新值
get key newValue
#返回key对应字符串的长度
strlen key
#删除key-value，返回数字类型
del key 
#自增/自减 如果 key 不存在，那么 key 的值会先被初始化为 0 ，然后再执行 INCR 操作 
incr key / decr key
#根据设置的增量，自增/自减
incrby key num / decrby key num
#字符串拼接
append key value
```

#### 5.1.1 string的应用场景 

> **1、String通常用于保存单个字符串或JSON字符串数据** 
>
> 2、**因String是二进制安全的**，所以你完全可以把一个图片文件的内容作为字符串来存储
>
> 3、**计数器**（常规key-value缓存应用。常规计数: 微博数, 粉丝数）
>
> **INCR等指令本身就具有原子操作的特性**，所以我们完全可以利用redis的INCR、INCRBY、DECR、DECRBY等指令来实现原子计数的效果。假如，在某种场景下有3个客户端同时读取了mynum的值（值为2），然后对其同时进行了加1的操作，那么，最后mynum的值一定是5。
>
> 不少网站都利用redis的这个特性来实现业务上的统计计数需求。

### 5.2 .hash类型

> Redis hash 是一个string类型的field和value的映射表，hash特别适合用于存储对象。 Redis 中每个 hash 可以存储 232 - 1 键值对（40多亿）
>
> 可以看成具有KEY和VALUE的MAP容器，该类型非常适合于存储值对象的信息， 
>
> **赋值语法：** 
>
>   HSET KEY  FIELD  VALUE    //为指定的KEY，设定FILD/VALUE  
>
>  **HMSET  KEY  FIELD VALUE [FIELD1,VALUE1]****…… 同时将多个 field-value (域-值)对设置到哈希表 key 中。** 
>
> ![img](https://note.youdao.com/yws/public/resource/91be86b8ffcf6144671e0d1bfe3c8952/xmlnote/OFFICE47DA25EADA0F4A5393A9B056C405CFE2/6260)
>
> 
>
> **取值语法：** 
>
>   HGET KEY FIELD   //获取存储在HASH中的值，根据FIELD得到VALUE
>
>   HMGET key field[field1]      //获取key所有给定字段的值 
>
>   HGETALL key                 //返回HASH表中所有的字段和值 
>
> ​	
>
> HKEYS key   //获取所有哈希表中的字段
>
> HLEN key   //获取哈希表中字段的数量
>
> 
>
> **删除语法：** 
>
>    HDEL KEY field1[field2]    //删除一个或多个HASH表字段 
>
> 
>
> **其它语法：**
>
> HSETNX key field value 
>
> 只有在字段 field 不存在时，设置哈希表字段的值
>
> 
>
> HINCRBY key field increment 
>
> 为哈希表 key 中的指定字段的整数值加上增量 increment 。
>
> 
>
> HINCRBYFLOAT key field increment  
>
> 为哈希表 key 中的指定字段的浮点数值加上增量 increment 。
>
> 
>
> HEXISTS key field  //查看哈希表 key 中，指定的字段是否存在

#### 5.2.1 应用场景

> Hash的应用场景：（存储一个用户信息对象数据）
>
> 1. **常用于存储一个对象**
> 2. **为什么不用string存储一个对象？**
>
> hash是最接近关系数据库结构的数据类型，可以将数据库一条记录或程序中一个对象转换成hashmap存放在redis中。
>
> 用户ID为查找的key，存储的value用户对象包含姓名，年龄，生日等信息，如果用普通的key/value结构来存储，主要有以下2种存储方式：
>
> ​    第一种方式将用户ID作为查找key,把其他信息封装成一个对象以序列化的方式存储，这种方式的缺点是，增加了序列化/反序列化的开销，并且在需要修改其中一项信息时，需要把整个对象取回，并且修改操作需要对并发进行保护，引入CAS等复杂问题。
>
> ​    第二种方法是这个用户信息对象有多少成员就存成多少个key-value对儿，用用户ID+对应属性的名称作为唯一标识来取得对应属性的值，虽然省去了序列化开销和并发问题，但是用户ID为重复存储，如果存在大量这样的数据，内存浪费还是非常可观的。
>
> **总结：**
>
> **Redis提供的Hash很好的解决了这个问题，Redis的Hash实际是内部存储的Value为一个HashMap，并提供了直接存取这个Map成员的接口**

### 5.3 list类型

>Redis列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素到列表的头部（左边）或者尾部（右边）一个列表最多可以包含 232 - 1 个元素 (4294967295, 每个列表超过40亿个元素) 
>
>类似JAVA中的**LinkedList**
>
>
>
>**命令**
>
>**赋值语法：** 
>
>LPUSH key value1 [value2]  //将一个或多个值插入到列表头部(从左侧添加)
>
>RPUSH key value1 [value2]  //在列表中添加一个或多个值(从右侧添加)
>
>LPUSHX key value   //将一个值插入到已存在的列表头部。如果列表不在，操作无效
>
>RPUSHX key value   //一个值插入已存在的列表尾部(最右边)。如果列表不在，操作无效。
>
>
>
>**取值语法：** 
>
>LLEN key      //获取列表长度
>
>LINDEX key index   //通过索引获取列表中的元素
>
>LRANGE key start stop  //获取列表指定范围内的元素
>
>
>
>描述： 返回列表中指定区间内的元素，区间以偏移量 START 和 END 指定。 其中 0 表示列表的第一个元素， 1 表示列表的第二个元素，以此类推。也可以使用负数下标，以 -1 表示列表的最后一个元素， -2 表示列表的倒数第二个元素，以此类推。
>
>**start:  页大小\*(页数-1)**  
>
>**stop : (页大小\*页数)-1**
>
>
>
>**删除语法：**
>
>LPOP key  移出并获取列表的第一个元素(从左侧删除)
>
>RPOP key  移除列表的最后一个元素，返回值为移除的元素(从右侧删除)
>
>
>
>BLPOP key1 [key2 ] timeout 
>
>移出并获取列表的第一个元素， 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止。
>
>**实例**
>
>redis 127.0.0.1:6379> BLPOP list1 100
>
>在以上实例中，操作会被阻塞，如果指定的列表 key list1 存在数据则会返回第一个元素，否则在等待100秒后会返回 nil 。
>
>
>
>BRPOP key1 [key2 ] timeout 
>
>移出并获取列表的最后一个元素， 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止。
>
>
>
>LTRIM key start stop   对一个列表进行修剪(trim)，就是说，让列表只保留指定区间内的元素，不在指定区间之内的元素都将被删除。
>
>
>
>
>
>**修改语法：**
>
>
>
>LSET key index value  通过索引设置列表元素的值
>
>LINSERT key BEFORE|AFTER world value 在列表的元素前或者后插入元素
>
>描述：将值 value 插入到列表 key 当中，位于值 world 之前或之后。
>
>
>
>**高级语法：**
>
>
>
>RPOPLPUSH source destination 
>
>移除列表的最后一个元素，并将该元素添加到另一个列表并返回	 **示例描述**：
>
>RPOPLPUSH a1  a2   //a1的最后元素移到a2的左侧
>
>RPOPLPUSH a1  a1  //循环列表，将最后元素移到最左侧
>
>
>
>BRPOPLPUSH source destination timeout 
>
>从列表中弹出一个值，将弹出的元素插入到另外一个列表中并返回它； 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止。

#### 5.3.1 应用场景 

>**项目常应用于：1、对数据量大的集合数据删减 2、任务队列** 
>
>
>
>1. **对数据量大的集合数据删减**
>
>列表数据显示、关注列表、粉丝列表、留言评价等…分页、热点新闻（Top5)等
>
>利用LRANGE还可以很方便的实现分页的功能,在博客系统中，每片博文的评论也可以存入一个单独的list中。
>
>
>
>1. **任务队列** 
>
>(list通常用来实现一个消息队列，而且可以确保先后顺序，不必像MySQL那样还需要通过ORDER BY来进行排序)
>
>
>
>任务队列介绍(生产者和消费者模式)： 
>
>在处理Web客户端发送的命令请求时，某些操作的执行时间可能会比我们预期的更长一些，通过将待执行任务的相关信息放入队列里面，并在之后对队列进行处理，用户可以推迟执行那些需要一段时间才能能完成的操作，这种将工作交给任务处理器来执行的做法被称为任务队列（task queue）。



## 6.Java连接Redis

> 开始在 Java 中使用 Redis 前， 我们需要确保已经安装了 redis 服务及 Java redis 驱动，且你的机器上能正常使用 Java。 Java的安装配置可以参考我们的 Java开发环境配置 接下来让我们安装 Java redis 驱动

### 6.1 在pom.xml中配置

```xml
        <!-- jedis -->
        <dependency>
            <groupId>redis.clients</groupId>
            <artifactId>jedis</artifactId>
            <version>2.9.0</version>
        </dependency>
```

### 6.2 java代码

```java
package com.redis.util;

import redis.clients.jedis.Jedis;
import redis.clients.jedis.JedisPool;
import redis.clients.jedis.JedisPoolConfig;

/**
 * 获取与redis连接的工具类
 */
public class RedisConnUtils {

    private static JedisPool pool = null;

    static {
        /**设置Redis 连接池*/
        JedisPoolConfig poolConfig = new JedisPoolConfig();
        poolConfig.setMaxTotal(5);
        poolConfig.setMaxIdle(1);
        //设置访问的主机号和端口号
        String host = "192.168.194.128";
        int port = 6379;
        pool = new JedisPool(poolConfig,host,port);
    }

    public static Jedis getConn() {
        Jedis jedis = pool.getResource();
        //设置redis登录密码
        jedis.auth("redis");
        return jedis;
    }

    public static void closeConn(Jedis jedis){
        jedis.close();
    }
}
```

### 6.3 Linux设置

```powershell
#查看已经开放的端口：
firewall-cmd --list-ports
#开启redis端口:
firewall-cmd --zone=public --add-port=6379/tcp --permanent
#重启防火墙
firewall-cmd --reload
```

## 7.RedisTemplate

> Spring data 提供了RedisTemplate模版 
>
> 它封装了redis连接池管理的逻辑，业务代码无须关心获取，释放连接逻辑；spring redis同时支持了Jedis，Jredis,rjc 客户端操作； 
>
> 在RedisTemplate中提供了几个常用的接口方法的使用，分别是 

```java
    @Nullable
    private ValueOperations<K, V> valueOps;
    @Nullable
    private ListOperations<K, V> listOps;
    @Nullable
    private SetOperations<K, V> setOps;
    @Nullable
    private ZSetOperations<K, V> zSetOps;
```

### 7.1 SpringData使用redisTemplate模版

```xml
        <!-- spring-data-redis -->
        <dependency>
            <groupId>org.springframework.data</groupId>
            <artifactId>spring-data-redis</artifactId>
            <version>2.1.8.RELEASE</version>
        </dependency>
```

### 7.2 对应实体Bean进行序列化操作 

```java
/**
 * 使用spring-data-redis
 * 实体类需要实例序列化接口
 */
public class Person implements Serializable {

    private String name;
    private int age;
```

### 7.3 编写相关配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--引入配置文件
    <bean id="placeholderConfigurer" class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
        <property name="order" value="1"/>
        <property name="ignoreUnresolvablePlaceholders" value="true"/>
        <property name="locations">
            <list>
                <value>classpath:redis.properties</value>
            </list>
        </property>
    </bean>
    -->

    <!--配置 jedis pool-->
    <bean id="jedisPoolConfig" class="redis.clients.jedis.JedisPoolConfig">
        <!-- 最大连接数 -->
        <property name="maxTotal" value="100"/>
        <!-- 最大空闲时间 -->
        <property name="maxIdle" value="20"/>
        <!-- 每次最大连接数 -->
        <property name="maxWaitMillis" value="1000"/>
        <property name="testOnBorrow" value="true"/>
    </bean>

    <!-- spring与redis的整合 -->
    <bean id="jedisConnectionFactory" class="org.springframework.data.redis.connection.jedis.JedisConnectionFactory">
        <property name="usePool" value="true"></property>
        <property name="hostName" value="192.168.194.128"></property>
        <property name="port" value="6379"></property>
        <property name="password" value="redis"></property>
        <property name="poolConfig" ref="jedisPoolConfig"></property>
    </bean>

    <!--redisTemplate-->
    <bean id="redisTemplate" class="org.springframework.data.redis.core.RedisTemplate">
        <property name="connectionFactory" ref="jedisConnectionFactory"/>
        <!-- 取消默认的序列化操作-->
        <property name="keySerializer">
            <bean class="org.springframework.data.redis.serializer.StringRedisSerializer"></bean>
        </property>
        <property name="valueSerializer">
            <bean class="org.springframework.data.redis.serializer.StringRedisSerializer"></bean>
        </property>
        <property name="hashKeySerializer">
            <bean class="org.springframework.data.redis.serializer.StringRedisSerializer"></bean>
        </property>
    </bean>

    <bean id="personService" class="com.redis.serviceImpl.PersonServiceImpl">
        <property name="redisTemplate" ref="redisTemplate"></property>
    </bean>
</beans>
```

