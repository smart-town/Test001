## 概述
Zokeeper 是一个开源的分布式协调服务，由雅虎创建，是 Google Chubby 的开源实现。分布式应用程序可以基于 zk 实现诸多功能如：数据发布/订阅、负载均衡、命名服务、分布式协调/通知、分布式锁等

zk可谓是目前使用最广泛的分布式组件了，其功能和职责单一，但却非常重要。
学习新的框架，先搞清楚它是什么？这是内涵。然后再介绍它能做什么，这是它的外延。内涵和外延共同定义框架本身，会对框架有较为深刻的理解，在应用层面知道如何应用。其次再搞清楚zk相关的理论基础，其目的是知道它是如何被发明的、能够借鉴以便自己今后能够用到。最后搞清楚zk的一些设计原理和细节。

### 什么是协调
在一个并发的环境中，我们为了避免多个运行单元对共享数据进行修改，造成数据损坏的情况出现，就必须依赖像锁这样的协调机制。让有的线程可以先操作这些资源，然后其他线程等待。对于进程内的锁来说，我们使用的各种语言都已经为我们提供了很多选择。对于在分布式环境中，即我们的程序运行在不同的机器上时，要如何实现协调呢？可能会说无非是将以前同一个进程中的一些方法通过网络实现在分布式环境中。的确如此，但是实际情况要复杂很多。因为网络是不可靠的。

比如，在同一个进程内。一个方法如果调用成功，那么就是成功。调用失败就是调用失败。在同一个进程里面，如果一个方法先调用就会先执行，但是在分布式环境中呢？由于网络的不可靠，你对一个服务的调用失败了并不表示一定失败了，可能调用成功了，但是响应返回的时候失败了。还有，A和B都去调用C服务。在时间上A还稍微前一些，但是最终结果却不一定是A的请求先到达。这些本来在同一个进程中的种种假设都需要我们重新思考，我们还要思考这些问题给我们设计和编码带来了哪些影响。还有，在分布式环境中为了提升可靠性，我们往往部署了多套服务，但是如何在多套服务中达到一致性，在同一个进程中很容易解决的问题到了分布式中都是大问题。

所以分布式协调远远比同一个进程中的协调复杂多。因此类似 zokeeper 这类的基础服务就应运而生。这些系统都在各个系统久经考验，它的可靠性，可用性都是经过理论和实践验证的。所以我们在构建一些分布式系统时，就可以以这类系统为起点构建我们的系统。这将节省不少成本，而且bug也会更少。

### zookeeper 是什么
在企业级应用系统中，随着信息化水平的不断提高，企业级应用变得越来越臃肿庞大，性能急剧下降。拆分系统是目前我们可选择的解决系统可伸缩性和性能问题的唯一行之有效的方法。但是拆分系统的同时也带来了系统的复杂性——各个系统不是孤立存在的，它们彼此之间需要协作和交互。这就是我们常说的分布式系统。各个子系统好比动物园中的动物，为了使各个子系统能够正常为用户提供统一服务，必须要一种机制来进行协调——Zookeeper（动物园管理员）



**来源？** 为什么会有zk呢？那就要从分布式开始说起，20世纪60年代，大型机被发明出来，凭借自身性能的优势成为了世界的主流。但是其也有一些致命的问题，比如造价昂贵、操作复杂等。特别是对大型机的人才培养成本十分之高。而另一方面PC机不断提升和普及，大家开始转向了使用小型机和普通PC来搭建分布式的计算机系统，以此降低成本。而后分布式系统不断火热起来。对于分布式系统的认识:一个软件系统分布在不同的网络计算机上，彼此之间仅仅通过消息传递进行通讯和协调的系统。分布式通常会有很多问题，诸如通讯异常、网络分区、节点故障等。为了解决分布式的诸多问题，zk作为apache hadoop的子项目发展了起来。**为分布式应用提供了高效可靠的分布式协调服务**

## 基本知识
### 1.Zk的组成
1. 文件系统
2. 通知机制
**文件系统**：zk维护一个类似文件系统的数据结构。每个子目录都被称为Znode。和文件系统一样，自由增加以及删除。唯一不同在于它可以存储数据。Znode分为4中类型：
    - PERSISTENT-客户端与zk断开连接后节点依旧存在
    - PERSISTENT_SEQUENTIAL-持久化顺序编号节点。
    - EPHEMERAL-临时目录节点，客户端与zk断开连接后该节点被删除
    - EPHEMERAL_SEQUENTIAL-临时顺序编号目录节点
**通知机制**：客户端注册监听它关心的目录节点。当目录节点发生变化时（数据改变、被删除、增加节点等），zk通知客户端
### 2.Zk能做些什么
1. 命名服务
2. 配置管理
3. 集群管理
4. 分布式锁
5. 队列管理
6. 分布式与数据复制




### Zk 的存储模型
zk 的数据存储使用的是结构化存储。结构化存储没有文件和目录的概念。里面的目录和文件被抽象成了节点。zk里面可以称为znode。并且每个znode都有一个唯一路径标识。znode中的数据可以有多个版本，比如某一路径下有多个数据版本，那么查询这个路径下的数据就要带上版本。节点不支持部分读写，而是一次性完成读写。

znode有两种类型：
1. 临时节点，短暂node的客户端会话结束时，zk会将该znode删除，短暂node不会有子节点
2. 持久节点，不依赖于客户会话，只有客户端明确表示删除该持久节点时才会被删除。

### Zk 角色
1. **领导者**：负责进行投票和发起决议，更新系统状态
2. **学习者**：包括跟随者follower和观察者observer，跟随者用于接收客户端请求并向客户端返回结果，在选举过程中参与投票。观察者可以接收客户端连接，将请求转发给leader，但是他不参与投票过程，只同步leader状态。它的目的是为了扩展系统提高读写速度。
3. **客户端**：请求发起方

### zk 核心算法
zk 的核心算法是 ZAB（原子消息广播协议），其核心如下：
所有的事务请求必须由一个全局唯一的服务器来协调处理，这样的服务器称为Leader服务器，而余下的服务器则成为 follower服务器。Leader服务器负责将一个客户端事务请求转换成一个事务提议，并将该提议分发给集群中所有的follower服务器。之后leader服务器需要等待所有follower服务器的反馈。一旦超过半数的follower进行了正确反馈后，那么leader就会再次向所有的follower服务器分发commit消息，要求将前一个提议提交。

领导选举过程：首先服务器将自身状态转换为looking，并向集群中所有服务器发起投票(myid,zxid)。接收其他服务器发送的投票，并进行处理。按照zxid最大的为leader，如果相同按照myid最大的作为leader。更新投票信息，再次向集群发送消息。统计投票结果，超过半数以上的投票即为leader。如果leader是自己，就修改状态为leading，否则变为following。

Zookeeper 是一个针对大型分布式系统的可靠协调系统。它提供的功能有：配置维护、名字维护、分布式同步、组织服务等。
它的目标就是封装好复杂易出错的关键服务，将简单易用的接口和性能高效、功能稳定的系统提供给用户。
#### 配置管理
在我们的应用中除了代码之外，还有一些就是配置。比如数据库连接等。一般我们使用配置文件的方式，在代码中引入这些配置文件。但是当我们只有一种配置、只有一台服务器，并且不经常修改的时候，使用配置文件是一种非常好的做法。但是如果我们的配置非常多，有很多服务器都需要这个配置，而且还可能是动态的话使用配置文件就不是个好主意了。这个时候往往需要寻找一种集中管理配置的方法。我们在这个集中的地方修改了配置，所有对这个配置感兴趣的都可以获取变更。比如我们可以将配置放到数据库中，然后所有需要配置的服务都去这个数据库读取配置。但是，因为很多服务的正常运行都非常依赖这个而配置，所以需要这个集中提供配置的服务具备很高的可靠性。一般我们可以用一个集群来提供这个配置服务。但是用集群提高可靠性，那么如何保证配置在集群中是一致的呢？这个时候就需要一种实现了一致性协议的服务了。zookeeper就是这种服务。它使用zab这种一致性协议来提供一致性。现在很多开源项目都使用zk来维护配置。
#### 用名字来服务
名字服务很好理解。通常通过网络访问一个系统，我们得知道对方的地址，但是IP地址对人非常不友好，这个时候我们通常使用域名来访问。但是对于我们提供服务的计算机是没有域名的，怎么办呢？如果我们每台机器都备份有一份域名到IP地址的映射，这个倒是能解决一部分问题。但是如果域名对应的IP发生了变化怎么办呢？于是我们有了DNS这个东西。我们只需要访问一个大家熟知的点，它就会告诉你这个域名对应的ip是什么。在我们的应用中也会存在很多类这样的问题，特别是在我们服务特别多的时候，我们在本地保存服务的地址会变得很不方便，但是如果我们只需要访问一个大家都熟知的统一点就可以了。
#### 分布式同步
ZkConfig 是为 zk 开发的配置服务工具包。能与现有的java系统进行良好集成。也可以使用与非java系统以独立进行运行。它用来解决系统集群中配置文件的实时同步。当任意一台服务器配置文件发生变化时，所有集群服务器配置文件实现同步更新。并且在不启动web容器的基础上实现内存配置对象的更新。
#### 分布式锁
zk是一个分布式协调服务，这样我们可以利用zk来协调多个分布式进程之间的活动。比如在一个分布式环境中，为了提高可靠性，我们集群的每台服务器都部署着相同的服务。但是，一件事情如果集群中的每个服务器都进行的话，那么相互之间就要进行协调，编程起来将十分复杂。而如果我们只让一个服务进行操作，那又存在单点。通常还有一种做法就是 **分布式锁**，在某个时刻只让一个进程去干活。当这台服务器出现问题时，立刻释放锁。fail over到另一台服务器。这在很多分布式系统中都是这样做的。这种设计有一个更好听的名字 **Leader 选举**。
#### 监控、集群管理
[原文地址](http://www.cnblogs.com/zlslch/p/7242110.html)