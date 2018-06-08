# TC_EpollServer

功能
* Server基类
* 注册协议解析器
* 注册逻辑处理器
* 注册管理端口处理器

## handle

服务的逻辑处理代码。`ServantHandle`继承于此。

```
    /**
     * 按name对handle分组，
     * 每组handle处理一个或多个Adapter消息
     * 每个handle对象一个线程
     */
    struct HandleGroup : public TC_HandleBase
    {
    // HelloServer.HelloObj, adapter
        string                      name;
        TC_ThreadLock               monitor;
        vector<HandlePtr>           handles;
        // HelloServer.HelloObj, adapter
        map<string, BindAdapterPtr> adapters;
    };
```

## BindAdapter

服务端口管理,监听socket信息。一个地址一个adapter
setName(`TestApp.HelloServer.HelloObjAdapte`)
setHandleGroupName(`TestApp.HelloServer.HelloObjAdapte`)

## NetThread

网络线程，主要负责监听`socket`，收发包等处理，一个线程一个epoll类，同时有`shutdown`和`notify`两个`socket`。用于接收对应事件的处理
* `shutdown`用于设置退出关闭的事件
* `notify`用于设置通知数据包发送和关闭某个连接的事件

`TC_EpollServer`创建的时候，指定内部的线程数（不允许大于15个），用来创建`NetThread`，使用第一个线程负责监听`adapter`，其余线程负责数据的收发
