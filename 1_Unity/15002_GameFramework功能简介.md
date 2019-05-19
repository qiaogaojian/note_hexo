# GameFramework 记录

## GameFrameworkEntry（框架入口）

    GameFrameworkEntry使用链表来维护所有的GameFrameworkModule（框架模块），并提供对GameFrameworkModule的各种相关操作以及框架版本号信息

1. 只有在获取模块时对应模块不存在才会创建模块

2. 创建模块时，根据模块优先级决定新模块在链表中的位置

3. 轮询模块时，根据优先级决定轮询顺序（即正向遍历链表调用OnUpdate）（在UGF中，由BaseComponent来调用轮询方法）

4. 所有的Manager类都需要继承GameFrameworkModule，并实现对应的IManager接口

## GameEntry（游戏入口）

    示例程序Start Force里包含两个GameEntry，一个是UGF的，一个是Start Force的。Start Force在场景GameFramework中有一个Game Framework物体，上面挂载了Start Force的GameEntry脚本作为游戏入口

1. UGF的GameEntry使用链表来维护所有的GameFrameworkComponent（框架组件），并提供各种相关操作。

2. 所有GameFrameworkComponent在Awake中调用UGF的GameEntry的RegisterComponent(GameFrameworkComponen)，将自身添加到UGF的GameEntry中的链表里

3. Start Force的GameEntry脚本持有所有GameFrameworkComponent的引用，被分成了3个部分，分别负责调用Start方法，初始化内置GameFrameworkComponent，初始化自定义GameFrameworkComponent

4. GameFramework物体下有预制体Builtin（由UGF框架提供，原名GameFramework），该预制体下的所有子物体分别挂载了所有的内置GameFrameworkComponent。所有内置GameFrameworkComponent都持有一个对应IManager接口的引用（可以视作GF的Manager在UGF中的代理或实现），在初始化时通过GameFrameworkEntry来获取实例

5. 自定义的GameFrameworkComponent需要另外创建一个空物体作为GameFramework的子物体，然后将自定义组件各自挂载到空物体下，作为其子物体

## FSM（有限状态机）与Procedure（流程）

    流程是贯穿游戏运行时整个生命周期的有限状态机。框架用流程来处理所有的事情，不同的流程负责不同的工作，流程的切换是用有限状态机来实现的

1. FSM模块主要由状态，状态机，状态机管理器三部分组成

2. 状态FsmState< T >维护一个事件码与事件处理方法的字典，并提供各种对应操作

3. 状态机Fsm继承FsmBase并实现IFsm< T >接口，维护该状态机的所有状态与状态机数据，并提供各种对应操作

4. 状态机管理器FsmManager维护所有状态机，并提供各种对应操作

5. Procedure模块主要由流程基类和流程管理器两部分组成

6. 流程基类ProcedureBase继承自FsmState< IProcedureManager >，可以理解为一种特殊的状态

7. 流程管理器ProcedureManager持有FsmManager与自身的Fsm的引用，并提供对流程的相关操作

8. 在UGF中的ProcedureComponent，主要负责读取创建好的流程类，然后调用ProcedureManager来创建并开始状态机

## Event（事件）

    Event是游戏逻辑监听、抛出事件的机制。

1. Event模块由事件参数，事件池，事件管理器三部分组成

2. 事件参数基类BaseEventArgs继承GameFrameworkEventArgs（还有另一种事件参数类也继承GameFrameworkEventArgs，那种事件不受Event模块管理）

3. 事件池EventPool< T >主要维护事件结点（Event）（Event是对BaseEventArgs的封装）的队列（该队列处理线程安全的事件抛出）与一个事件码与事件处理方法的字典，并提供各种对应操作

4.事件管理器EventManager是对EventPool< T >里各种操作的代理

## 任务（Task）

    Task功能主要负责管理Web请求、资源的下载或加载的任务的执行

1. Task功能主要由任务，任务代理，任务池三部分组成

2. 任务（ITask）存储了任务执行需要的数据

3. 任务代理（ITaskAgent< T >）是对ITask的代理类，提供对任务的各种对应操作

4. 任务池TaskPool< T >负责维护三种容器（可用任务代理，工作中任务代理，等待任务），并提供各种对应操作

## DataNode（数据结点）

    DataNode将任意类型的数据以树状结构的形式进行保存，用于管理游戏运行时的各种数据,数据结点的使用非常灵活，有以下三种使用方式：
- 使用数据结点组件，直接通过绝对路径获取或设置数据；
- 使用数据结点组件，通过参照某个数据结点和相对路径获取或设置数据；
- 使用数据结点组件获取数据结点后，通过数据结点的接口进行更多操作。

1. DataNode模块由数据结点和数据结点管理器两部分组成

2. 数据结点（DataNode）存储数据以及父结点、子结点的相关信息

3. 数据结点管理器（DataNodeManager）管理根结点，并提供数据结点的相关操作

## ObjectPool（对象池）

    ObjectPool提供对象缓存池的功能，避免频繁地创建和销毁各种游戏对象，提高游戏性能。

1.ObjectPool模块主要由对象基类，内部对象，对象池，对象池管理器三部分组成

2.对象基类（ObjectBase）是所有需要由ObjectPool模块管理的对象的父类

3.内部对象（Object< T >）存储ObjectBase相关数据，并提供获取与回收的方法

4.对象池（ObjectPool< T >）使用链表维护池子里的所有Object< T >，并提供各种相关操作

5.对象池管理器（ObjectPoolManager）使用字典维护所有ObjectPool< T >，并提供各种相关操作

## Download（下载）

    Download模块提供下载文件的功能，支持断点续传，并可指定允许几个下载器进行同时下载。更新资源时会主动调用此模块

1. DownLoad模块主要由下载任务，下载代理辅助器，下载任务代理，下载管理器，下载事件五个部分组成

2. 下载任务（DownloadTask）实现了ITask接口，保存了下载任务的相关数据

3. 下载代理辅助器（IDownloadAgentHelper）负责进行实际的下载逻辑处理（在UGF中提供了默认的实现DefaultDownloadAgentHelper，使用WWW类进行下载）（下载过程中通过3个委托向DownloadAgent通知下载事件）

4. 下载任务代理（DownloadAgent）实现了ITaskAgent接口，负责处理下载任务，并持有一个IDownloadAgentHelper，调用其中的方法进行实际的下载。（下载过程中通过4个委托向DownloadManager通知下载事件）

5. 下载管理器（DownloadManager）维护一个DownloadTask的TaskPool，对外提供DownloadTask的相关操作（下载过程中通过4个委托将DownloadAgent的下载事件通知给UGF的DownloadComponent，然后由其派发4个全局事件）

6. 下载事件分为三部分，一部分是DownloadManager里的事件，另一部分是DownloadAgentHelper里的事件，这两部分定义在GF里，是模块局部事件，第三部分则是UGF里定义的全局事件,由DownloadComponent接收到模块局部事件后进行派发

## WebRequest（Web请求）

    WebRequest模块提供使用短连接的功能，可以用 Get 或者 Post 方法向服务器发送请求并获取响应数据，可指定允许几个 Web 请求器进行同时请求

1. WebRequest模块主要由由Web请求任务，Web请求代理辅助器，Web请求任务代理，Web请求管理器，Web请求事件五个部分组成

2. Web请求任务（WebRequestTask）实现了ITask接口，保存了Web请求任务的相关数据

3. Web请求辅助器（IWebRequestAgentHelper）负责进行实际的请求逻辑处理（在UGF中提供了默认的实现DefaultWebRequestAgentHelper，使用WWW类进行请求）（请求过程中通过2个委托向WebRequestAgent通知请求事件）

4. Web请求任务代理（WebRequestAgent）实现了ITaskAgent接口，负责处理请求任务，并持有一个IWebRequestAgentHelper，调用其中的方法进行实际的请求。（请求过程中通过3个委托向WebRequestManager通知请求事件）

5. Web请求管理器（WebRequestManager）维护一个WebRequestTask的TaskPool，对外提供WebRequestTask的相关操作（请求过程中通过3个委托将WebRequestAgent的请求事件通知给UGF的WebRequestComponent，然后由其派发3个全局事件）

6. 下载事件分为三部分，一部分是WebRequestManager里的事件，另一部分是WebRequestAgentHelper里的事件，这两部分定义在GF里，是模块局部事件，第三部分则是UGF里定义的全局事件,由WebRequestComponent接收到模块局部事件后进行派发

## ReferencePool（引用池）

    ReferencePool功能负责管理对象的引用

1. ReferencePool功能主要由引用接口，引用集合，引用池三部分组成

2. 只有实现了引用接口（IReference）的类才能被ReferencePool管理，在框架中由BaseEventArgs实现，也就是说ReferencePool主要拿来管理对BaseEventArgs的引用

3. 引用集合（ReferenceCollection）维护一个IReference的队列，并提供该对象的各种信息或相关操作

4. 引用池（ReferencePool）维护一个ReferenceCollection的字典，并提供各种相关的操作

[资源](http://gameframework.cn/resource)

[GameFramework篇：个人笔记汇总](https://www.lfzxb.top/gameframework_all/)

[GameFramework框架个人笔记汇总](https://blog.csdn.net/qq_15020543/article/details/86766583)