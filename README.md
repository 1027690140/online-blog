

### 使用技术：

1. springboot + mybaitisplus + springMVC 
1. JWT + redis + springSecurity
1. rocketMQ
1. 部署： docker

---


### 项目介绍：
1、jwt + redis
token令牌的登录方式，访问认证速度快，session共享，安全性
redis做了令牌和用户信息的对应管理，进一步增加了安全性，并给登录用户做了缓存

2.自定义拦截器，配合redis，从而防止暴力登录

3、threadLocal使用了保存用户信息，请求的线程之内，可以随时获取登录的用户，做了线程隔离，在使用完ThreadLocal之后，做了value的删除，防止了内存泄漏（因为ThreadLocal 中的 key 是弱引用，value 是强引用。一个线程可能有时候很久都不会被销毁，但是这时候只有弱引用的 key 会被回收，value 由于是强引用，由于线程还存在，他就会存在，但是 value 已经没有用了，这个时候就造成了浪费。）
#### 为什么要使用 [ThreadLocal](https://so.csdn.net/so/search?q=ThreadLocal&spm=1001.2101.3001.7020) 进行登录时处理用户信息？而非普通变量？：
假如有两个用户 A 和 B，他们分别进行登录，并且他们的每次请求都会带有自己的 token，在请求到达 controller 之前（preHandle() 中），每次都会被会被拦截器进行拦截，提取出当前 token 中的用户信息（比如 userId），认证通过以后在 service 中就可以通过 Contenxt 类获取提取出来的用户信息。
方式1：（1）使用普通变量，例如 String
方式2：（2）使用 ThreadLocal 类进行存储
 对比：第一种方式看似运行时和第二种没有区别，但是在**高并发**的时候，由于 USER_ID 的值的设置和 USER_ID 的值的获取是两次操作，那么很显然设置和获取不是一个原子性的操作，这时候肯定会发生并发问题，即：A 刚设置了值，还没有等到 A 取值，B 就将这个 String 类型的 USER_ID 设置成了自己的信息。这时候 A 再进行取值，取到的就是 B 的值。（类似于不可重复读）
第二种方式的话，很显然就解决了这个问题，因为他们都是操作的自己线程内的 USRE_ID，各个线程之间互不影响，所以这个时候，完全不会混乱。
 
4·、线程安全-update table set value = newValue where id=1 and value=oldValue

5、线程池应用非常广，面试7个核心参数（对当前的主业务流程无影响的操作，放入线程池执行）

6·用springsecurity 做一了个权限系统  

7·统一日志记录（由aop实现）

8.对文章的加载进行了统一缓存处理（由aop实现），同时使用rocketMQ解决了缓存不一致问题

### 思考优化：

评论数据，可以考虑放入mongodb当中   。
文章可以放入Elasticsearch当中，便于后续中文分词搜索。从而完成全文检索功能
可以上传静态 文件上传到  云服务器，从而降低我们自身应用服务器的带宽消耗
 
### 项目截图：

![a6140f3851da469f95b0ad3d1946760a.png](https://cdn.nlark.com/yuque/0/2022/png/29261914/1657285657857-c9607e92-983b-413d-a9fa-41bb8103ab8f.png#clientId=u434be5a3-28d2-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=534&id=u1e64c14f&margin=%5Bobject%20Object%5D&name=a6140f3851da469f95b0ad3d1946760a.png&originHeight=587&originWidth=1239&originalType=binary&ratio=1&rotation=0&showTitle=false&size=64107&status=done&style=none&taskId=u6fa30c84-8d90-41dd-af61-27dbc4bda8a&title=&width=1126.3636119503626)
文章分类
![QQ拼音截图20220706155511.png](https://cdn.nlark.com/yuque/0/2022/png/29261914/1657242096016-56c6aaa3-3ce4-4fd6-aae1-b5842c3fa022.png#clientId=udc6b31c8-850e-4&crop=0&crop=0&crop=1&crop=1&from=ui&id=u09e0a619&margin=%5Bobject%20Object%5D&name=QQ%E6%8B%BC%E9%9F%B3%E6%88%AA%E5%9B%BE20220706155511.png&originHeight=441&originWidth=1651&originalType=binary&ratio=1&rotation=0&showTitle=true&size=109605&status=done&style=none&taskId=u981466e5-929c-40eb-aad4-d3f7dcede0b&title=%E6%9D%83%E9%99%90%E7%B3%BB%E7%BB%9F "权限系统")
### 项目地址：[https://github.com/1027690140/online-blog.git](https://github.com/1027690140/online-blog.git)
