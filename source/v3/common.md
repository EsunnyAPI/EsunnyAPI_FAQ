# 常见问题-共通

<!-- toc --> 


### API基本上都需要SessionID,请问这个SessionID是从哪里可以得到？在那个API方法里传呢？

 有API和Notify两个接口类，API是提供给上层App的调用接口，Notify相当于Spi，由上层App实现。
每一次调用认为是一个Session,有唯一的一个SessionID。
App调用API请求类接口时，传入SessionID指针，API会返回一个值。
当服务器应答数据回来时，API通过Notify接口把对应的SessionID和应答数据一同返回。

----------

### 关于API的线程
 
 API的工作线程有：

- 线程1：缓存数据查询请求与应答线程，例如合约、品种信息等
- 线程2：Socket接收线程，用于返回服务器的实时应答
- 线程3：关键操作日志记录线程
- 线程4：API日志记录线程

----

### 登陆时，有哪些相关接口，调用时序是怎么样的？

调用接口：

- `SetHostAddress` :用于设置服务器IP、端口
- `Login`：填入用户名密码等信息，执行登陆

回调接口： 

- `OnConnect`：已经于服务器建立连接（未登录）
- `OnRspLogin`：如果errorCode为0，表示登陆成功，开始进行基础数据初始化；否则，返回错误原因，需重新登陆。
- `OnAPIReady`：API基础数据初始化完成，可以进行业务接口调用


``` sequence
Application->TapAPI: SetHostAddress
Application->TapAPI: Login
TapAPI->Application: OnRspLogin
Note left of TapAPI: 如果登陆成功\n继续返回OnAPIReady
TapAPI->Application: OnAPIReady
```

---

### 关于错误码

易盛的接口返回的错误码分两类：

- 第一类是易盛自己的错误代码，只返回了错误代码，具体错误信息可以参考API文档或头文件
- 第二类是交易所的代码和错误信息

**TODO:** 添加详细解释（图）


---
### 多个API实例会相互影响吗？

除了日志通过同一个日志实例，记在同一个文件中，其他的没有影响

---
### demo程序是用哪一版的VS创建的？
VS2012
