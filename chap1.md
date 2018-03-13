# 定义分布式系统

为了定义分布式系统，需要关注以下特征：

* 资源共享

  这关系到使用系统资源的可能性。比如，存储、计算、其他服务等。

* 扩展性

  这关系到系统的扩展和改进的可能性，包括硬件和软件层面。

* 并发性

  这关系到系统能否供多用户同时使用，完成不同任务。

* 性能和伸缩性

  系统负荷增加时，保证系统响应时间。

* 容错性

  在系统单个部分出错，或者操作不当时仍然保持可用。

* API 抽象性

  确保系统单个组件对使用端隐藏实现。

###分布式系统常见错误假设

* 假设1 网络是可靠的

  事实上用以连接不同组件的网络常常因为软件内部错误，或外部原因（比如断电）而出现失败。

* 假设2 网络无延时

  分布式系统的用户可以在全球任何地方接入系统。将数据移动到另一个地方需要花费一定的时间。另外服务的网络质量也影响应用延时。

* 假设3 带宽是无限的

  纵然网络带宽已经在全球范围内得到改进，但全世界范围的带宽也不是一致的。网络带宽取决于网络传输介质。

* 假设4 网络是安全的

  网络从来都不是安全的。

* 假设5 拓扑不会改变

  事实上系统拓扑从来就不是稳定的。组件随时会被增加或者移除。系统必须能够处理这些变化。

* 假设6 总有一个无所不能的管理员

  分布式系统从不是独立功能的。功能都是由不同组件互相交互实现的。因此统一管理非常困难。

* 假设7 无传输消耗

  任何传输都有消耗（计算资源，网络资源等）。

* 假设8 网络是同质的

  网络是有许多不同的实体构成的。因此一个应用如果功能正常，就必须与大量的组件交互。这些组件基于不同的网络介质、不同操作系统，甚至是不通编程语言实现的。

分布式系统的设计者必须时刻牢记以上事实。除此之外，下一个棘手的问题是，如何组合不同组件的计算实体或者独立程序，以实现功能。在设计或实现这些协同逻辑时，开发或设计人员常常陷入泥潭。这便是Zookeeper的设计和实现动机。

###Zookeeper 支持的常见分布式协作任务：

* 配置管理
* 服务命名
* 分布式同步，如锁，和隔离 
* 集群成员操作，如感知节点的加入和删除。

# 分布式系统协作的挑战

* 配置管理

* 服务发现

* 伸缩性与协作的矛盾

  横向扩展系统，提升系统的可用性。但系统部分崩溃的风险随之增加。

# Apache ZooKeeper 简介

分布式、高弹性，中心化协作服务，运行在称为Zookeeper 乐团的集群服 C务上。

用Java实现，支持 C, Java, Perl, Python 客户端。社区贡献了 Go, Scala, Erlang 等客户端。

在 Yahoo, Twitter, Netflix, Facebook 等公司广泛使用。



# 快速上手

### 下载

```shell
$ wget http://www.gtlib.gatech.edu/pub/apache/zookeeper/stable/zookeeper-
3.4.6.tar.gz
$ ls -alh zookeeper-3.4.6.tar.gz

-rw-rw-r-- 1 saurav saurav 17M Feb 20  2014 zookeeper-3.4.6.tar.gz

```

###安装

```shell
$ tar -C /usr/share -zxf zookeeper-3.4.6.tar.gz
$ cd /usr/share/zookeeper-3.4.6/
$ ls

bin      CHANGES.txt
README_packaging.txt
3.4.6.jar.md5

build.xml      conf
NOTICE.txt      README.txt      src       zookeeper-3.4.6.jar.asc
zookeeper-3.4.6.jar.sha1

```

导入系统变量：

```shell
$ export ZK_HOME=/usr/share/zookeeper-3.4.6
```

### 配置

```shell
$ cat conf/zoo.cfg
tickTime=2000
dataDir=/var/lib/zookeeper
clientPort=2181
```

* `tickTime`: 毫秒单位，系统基本计时单位，客户端-服务端心跳检查时间。
* `dataDir`: 存储 zookeeper 内存状态的路径。包括数据库快照、事务日志。路径不会自动创建。
* `clientPort`: 服务端监听端口。

### 启动 Zookeeper 服务

```shell
$pwd
/usr/share/zookeeper-3.4.6/bin
$ls
README.txt  zkCleanup.sh  zkCli.cmd  zkCli.sh  zkEnv.cmd  zkEnv.sh
zkServer.cmd  zkServer.sh

```

```shell
$ ./zkServer.sh
JMX enabled by default
Using config: /usr/share/zookeeper-3.4.6/bin/../conf/zoo.cfg
Usage: ./zkServer.sh
{start|start-foreground|stop|restart|status|upgrade|print-cmd}
```

```shell
$ zkServer.sh start
JMX enabled by default
Using config: /usr/share/zookeeper-3.4.6/bin/../conf/zoo.cfg
Starting zookeeper ... STARTED
```

```shell
$ zkServer.sh status
JMX enabled by default
Using config: /usr/share/zookeeper-3.4.6/bin/../conf/zoo.cfg
Mode: standalone
```

```shell
$ zkServer.sh stop
JMX enabled by default
Using config: /usr/share/zookeeper-3.4.6/bin/../conf/zoo.cfg
Stopping zookeeper ... STOPPED
```

```shell
$ zkServer.sh status
JMX enabled by default
Using config: /usr/share/zookeeper-3.4.6/bin/../conf/zoo.cfg
Error contacting service. It is probably not running.
```

### 使用基于 java 的 shell 连接 Zookeeper

```shell
$ zkCli.sh -server localhost:2181
Connecting to localhost:2181
...............
...............
Welcome to ZooKeeper!
JLine support is enabled
...............
WATCHER::
WatchedEvent state:SyncConnected type:None path:null
[zk: localhost:2181(CONNECTED) 0]
```

```shell
[zk: localhost:2181(CONNECTED) 0] help
ZooKeeper -server host:port cmd args
connect host:port
  get path [watch]
  ls path [watch]
  set path data [version]
  rmr path

  delquota [-n|-b] path
  quit
  printwatches on|off
  create [-s] [-e] path data acl
  stat path [watch]

  close
  ls2 path [watch]
  history
  listquota path
  setAcl path acl
  getAcl path
  sync path
  redo cmdno
  addauth scheme auth
  delete path [version]
  setquota -n|-b val path
```

```shell
[zk: localhost:2181(CONNECTED) 1] ls /
[zookeeper]
```

```shell
[zk: localhost:2181(CONNECTED) 2] create /HelloWorld ""
Created /HelloWorld
[zk: localhost:2181(CONNECTED) 3] ls /
[zookeeper, HelloWorld]
[zk: localhost:2181(CONNECTED) 4] delete /HelloWorld
[zk: localhost:2181(CONNECTED) 5] ls /
[zookeeper]
```

### 配置多节点集群

增加配置项：

```shell
tickTime=2000
dataDir=/var/lib/zookeeper
clientPort=2181
initLimit=5
syncLimit=2
server.1=zoo1:2888:3888
server.2=zoo2:2888:3888
server.3=zoo3:2888:3888
```

- `initLimit`: zookeeper集群中的包含多台server, 其中一台为leader, 集群中其余的server为follower. initLimit参数配置初始化连接时, follower和leader之间的最长心跳时间. 此时该参数设置为5, 说明时间限制为5倍tickTime, 即5*2000=10000ms=10s.
- `syncLimit`: 该参数配置leader和follower之间发送消息, 请求和应答的最大时间长度. 此时该参数设置为2, 说明时间限制为2倍tickTime, 即4000ms.
- `2888` 点对点通信，如 followers 和 leaders 之间通信。
- `3888` 用于leader节点选举。

### 创建 myid

在 `dataDir`下创建一个名为`myid`的文件，里面有一个数字（1-255），每个节点有不同的myid。

### 启动服务实例

在每个节点上执行：

```shell
${ZK_HOME}/bin/zkServer.sh start
```

查看状态：

```shell
${ZK_HOME}/bin/zkServer.sh status
JMX enabled by default
Using config: /usr/share/zookeeper-3.4.6/bin/../conf/zoo.cfg
Mode: follower
```

```shell
[zoo2] # ${ZK_HOME}/bin/zkServer.sh status
JMX enabled by default
Using config: /usr/share/zookeeper-3.4.6/bin/../conf/zoo.cfg
Mode: leader
```

```shell
[zoo3] # ${ZK_HOME}/bin/zkServer.sh status
JMX enabled by default
Using config: /usr/share/zookeeper-3.4.6/bin/../conf/zoo.cfg
Mode: follower

```

在这个例子中，zoo2被选举为 leader, 其他两个节点是 follower。

客户端连接：

```shell
$ zkCli.sh -server zoo1:2181,zoo2:2181,zoo3:2181
Connecting to zoo1:2181, zoo2:2181, zoo3:2181
......... ...
Welcome to ZooKeeper!
......... ...
[zk: zoo1:2181,zoo2:2181,zoo3:2181 (CONNECTED) 0]
```

### 单机多节点模式

在同一台机器上也可以启动多节点模式的zookeeper:

```shell
tickTime=2000
initLimit=5
syncLimit=2
dataDir=/var/lib/zookeeper
clientPort=2181
server.1=localhost:2666:3666
server.2=localhost:2667:3667
server.3=localhost:2668:3668
```

注意：

* server.x=localhost:2666:3666  第二段和第三段的端口号不能重复
* clientPort 不能重复
* 每个实例都有自己的dataDir
* 三个配置文件 zoo1.cfg, zoo2.cfg, zoo3.cfg，放在 `${ZK_HOME}/conf`下
* 在`/var/lib/zookeeper` 下创建3个文件夹 `zoo1, zoo2, zoo3`

第一个节点的配置文件：

```shell
tickTime=2000
initLimit=5
syncLimit=2
dataDir=/var/lib/zookeeper/zoo1
clientPort=2181
server.1=localhost:2666:3666
server.2=localhost:2667:3667
server.3=localhost:2668:3668
```

第二个第三个：

```shell
tickTime=2000
initLimit=5
syncLimit=2
dataDir=/var/lib/zookeeper/zoo2
clientPort=2182
server.1=localhost:2666:3666
server.2=localhost:2667:3667
server.3=localhost:2668:3668
```

```shell
tickTime=2000
initLimit=5
syncLimit=2
dataDir=/var/lib/zookeeper/zoo3
clientPort=2183
server.1=localhost:2666:3666
server.2=localhost:2667:3667
server.3=localhost:2668:3668
```

* 创建 `myid`

  ```shell
  $ echo 1 > /var/lib/zookeeper/zoo1/myid
  $ echo 2 > /var/lib/zookeeper/zoo2/myid
  $ echo 3 > /var/lib/zookeeper/zoo3/myid
  ```

  ​

* 启动

  ```shell
  $ ${ZK_HOME}/bin/zkServer.sh start ${ZK_HOME}/conf/zoo1.cfg
  $ ${ZK_HOME}/bin/zkServer.sh start ${ZK_HOME}/conf/zoo2.cfg
  $ ${ZK_HOME}/bin/zkServer.sh start ${ZK_HOME}/conf/zoo3.cfg
  ```

  ​

