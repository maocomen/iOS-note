# iOS网络层设计方案探讨

[[iOS应用架构谈 网络层设计方案](https://casatwy.com/iosying-yong-jia-gou-tan-wang-luo-ceng-she-ji-fang-an.html)]casa大神的网络层设计方案

网络层这块儿，一直感觉是个很神秘莫测的东西，看了casa大神的blog，也跟基友比比了好久，一直存在争议，在这里，记录一下我个人的一点想法。

从我们点击view发起网络请求，最终到网络请求落地UI的更新，大概要进行这么几步，

```mermaid
graph LR
A[点击View] --> B[发起网络请求]
B --> C[网络请求回调]
C --> D[数据处理]
D --> E[数据返回给View]
E --> F[View刷新]
```

更细的我们先不说，按最基本的流程走的话，大概是这么几步。

这个流程其实可以进行细分，view层，请求层，数据层。

* view层，view层只做最简单的逻辑，点击，从数据层获取数据，然后刷新
* 数据层，数据层负责返回数据给view层，view层调用数据层的数据接口，由数据层来决定是取缓存的数据还是发起网络请求更新数据


* 请求层，这一层主要是做第三方网络请求框架的封装，Get，Post，Socket之类的底层通信都可以封装在这一层，将别人的转化为我们自己的东西，就算以后网络请求框架换了或者怎样，都不会影响其他层的处理。

这三层只是最简单的三层，如果有需要的话，我们在这个基础之上再加层就够了

按CTNetworking中，我们可以看到，view层是通过CTAPIBaseManager进行网络请求的发起的，CTAPIBaseManager这个类里面，其实还是有蛮多东西的

* interceptor，拦截器，进行请求前，请求后，成功和失败之后的数据拦截（拦截到某个时刻，执行这个时刻该做的一些操作）
* validator，验证器，做请求入参格式校验以及回参校验
* cache，缓存管理器
* DataReformer，字面上看，是数据改革者，根据不同的manager对数据进行不同的处理
* CTApiProxy，代理人，其实就是请求层，专门发起网络请求接受回调的。这个东西其实是不面向业务开发者的，业务开发者只需要通过manager进行调度就够了，也就是loadData:Params:里会自行调用请求层。





