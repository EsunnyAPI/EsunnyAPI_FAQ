# 常见问题-共通

# 关于启明星（9.0）API的授权

## 为什么需要授权？

为了保证账户安全，启明星系统默认不开通用户的API访问权限。

如果客户想要通过API获取行情或者进行交易，必须向期货公司申请开通自己账户对应的API权限。

## 如何获取授权文件？

**模拟环境：**

可以使用公共的测试授权码：`启明星API测试授权码_2025年到期.txt`，软件授权号：Demo_Test

获取方式：[开发者中心](https://user.esunny.com.cn/index.php?m=member&c=index&a=nouklogin&gid=2)或[启明星API开发者交流群](http://jq.qq.com/?_wv=1027&k=2DRexiQ)


**实盘环境：**
实盘授权码，请到易盛官网 [开发者中心](https://user.esunny.com.cn/index.php?m=member&c=index&a=nouklogin&gid=2) 进行申请。


## 授权码怎么用？
以实盘授权为例：

![授权文件使用说明](../images/v9_shou_quan_shi_li.png)

`软件授权号`：由期货公司使用，用于在后台配置用户的API授权。

`授权码`：有软件开发商使用，在初始化API时使用。

*注意：软件开发商使用的`授权码`为长度512字节的16进制字符串（无空格、无回车）。*


软件开发商向期货公司申请开通账号的API权限时，需要提供:
- 软件授权号
- 需要开通权限的账号（多个账号需分别说明）

同一个授权码可以在多个期货公司使用，一个软件开发商只需申请一个授权即可。

----------

# API基本上都需要SessionID,请问这个SessionID是从哪里可以得到？在那个API方法里传呢？

有API和Notify两个接口类，API是提供给上层App的调用接口，Notify相当于Spi，由上层App实现。
 
每一次调用认为是一个Session,有唯一的一个SessionID。

App调用API请求类接口时，传入SessionID指针，API会返回一个值。

当服务器应答数据回来时，API通过Notify接口把对应的SessionID和应答数据一同返回。

----------

# 关于API的线程
 
 API的工作线程有：

- 线程1：缓存数据查询请求与应答线程，例如合约、品种信息等
- 线程2：Socket接收线程，用于返回服务器的实时应答
- 线程3：关键操作日志记录线程
- 线程4：API日志记录线程

----

# 登陆时，有哪些相关接口，调用时序是怎么样的？

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
# 多个API实例会相互影响吗？

除了日志通过同一个日志实例，记在同一个文件中，其他的没有影响

---
# demo程序是用哪一版的VS创建的？
VS2012

---
# 返回错误码-10004是什么原因？

错误码-10004代表输入错误的:TAPICallOrPutFlagType，即错误的看涨看跌字段。

通常是由于对CallOrPutFlag2字段未赋值，当合约为单腿时，需要将此字段赋值TAPI_CALLPUT_FLAG_NONE。

类似的，其他字符型枚举变量（类型名通常以Type结尾），都需要这么处理。


