# API整体介绍

-----


## 易盛金融衍生品系统

易盛推出的金融衍生品系统，目前市场上在用的有`3.0`、`8.0`、`9.0`三个平台，各个平台有自己对应的行情与交易API。

- `3.0`：国际金融衍生品交易分析系统, 用于外盘交易。
- `8.0`：内盘期货交易管理系统，用于内盘交易。
- `9.0`：启明星期货期权交易平台，有内盘的行情和交易，以及外盘行情。

对于**内盘**开发者，
- 可先联系期货公司获取目前正在使用的易盛系统版本号：
 - 使用9.0平台对应的9.0API。
 - 使用8.0平台对应8.0API。

对于**外盘**开发者：
- 目前交易只能用3.0API
- 行情可以用3.0也可以用9.0（以期货公司部署的行情后台系统为准）。

-----

## 各平台API库命名

### 3.0 API
|系统|行情|交易|
|--|--|--|
|Windows|EsunnyQuot.dll|ForeignTradeApi.dll|
|Linux|libEsunnyQuot.so|libForeignTradeApi.so|

### 8.0 API
|系统|行情|交易|
|--|--|--|
|Windows|EsunnyApi.dll|EsunnyApi.dll|
|Linux|无|无|

### 9.0 API
|系统|行情|交易|
|--|--|--|
|Windows|TapQuoteAPI.dll|TapTradeAPI.dll|
|Linux|TapQuoteAPI.so|TapTradeAPI.so|


------

## API获取方法
### 1.易盛网站

1.进入易盛官网 [www.esunny.com.cn](http://www.esunny.com.cn)

2.网站右上角有`API接入`


### 2.开发者交流QQ群
启明星API开发者交流群 253509842

群里也有相应的开发文档。但分类不太清晰，建议从开发者中心网站获取。



------

##模拟环境

易盛现在对外提供的API模拟环境有：

### **内盘**：

||9.0 行情|9.0 交易|
|--|:--:|:--:|
|联通|123.15.58.21:6161|123.15.58.21:6160|
|电信|222.88.40.170:6161|222.88.40.170:6160|

行情账号：用户名ESUNNY, 密码Es123456

交易账号：[点击此处注册](http://www.esunny.com.cn/index.php?m=content&c=index&a=lists&catid=49)

### **外盘**：

||3.0 行情|3.0 交易|9.0 行情|
|--|:--:|:--:|:--:|
|联通|123.15.58.21:3331|123.15.58.21:7070|123.15.58.21:7171|
|电信|222.88.40.170:3331|222.88.40.170:7070|222.88.40.170:7171|

3.0行情账号：用户名ESUNNY, 密码Es123456 (该模拟环境为过期的虚拟合约，非实盘行情)

3.0交易账号：[点击此处注册](http://www.esunny.com.cn/index.php?m=content&c=index&a=lists&catid=50)

9.0行情账号：用户名ES，密码123456 (该模拟环境基于实盘行情调整)


----

##客户端

易盛各个平台都有相应的客户端。
API使用者，也可以同时登录客户端进行调试、验证。

- 内盘交易/行情： [moni_epolestar.zip](http://www.esunny.com.cn/uploadfile/software/EsunnyTap9/moni_epolestar.zip )
- 外盘 3.0交易/9.0行情： [EsunnyWP_APIClient_9.0.zip](http://www.esunny.com.cn/uploadfile/software/EsunnyWP_APIClient_9.0.zip)
- 外盘3.0行情：[itrader3.0.zip](http://www.esunny.com.cn/uploadfile/software/itrader3.0.zip)



