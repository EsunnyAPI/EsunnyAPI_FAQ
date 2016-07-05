# 行情

----

#错误码
```cpp
const int ESUNNY_QUOT_ERR_SUCCESS            =  0; //成功
const int ESUNNY_QUOT_ERR_CONNFAIL           = -1; //连接失败
const int ESUNNY_QUOT_ERR_SETSOCK            = -2; //设置连接属性失败
const int ESUNNY_QUOT_ERR_NOCONN             = -3; //未连接
const int ESUNNY_QUOT_ERR_NOLOGGED           = -4; //未登录
const int ESUNNY_QUOT_ERR_NOCONTRACT         = -5; //未找到此合约代码
const int ESUNNY_QUOT_ERR_DATEINCORRECT      = -6; //日期不正确
const int ESUNNY_QUOT_ERR_SUBNUMEXCEED       = -7; //行情订阅数量超限
const int ESUNNY_QUOT_ERR_SUBFREQUENCYEXCEED = -8; //行情订阅频率超限

```
---

#流程
1. CreateEsunnyQuotClient 创建一个行情API实例,并设置回调函数接口
2. InitSecretKey 初始化密钥
3. Connect 连接行情服务器
4. Login 登陆行情服务器，完成初始化阶段
5. OnRspMarketInfo 接收可以订阅的所有行情合约
6. 订阅从第5步中接收的合约

---

#批量订阅合约

- 先循环调用AddReqStk,将订阅的行情添加在缓存中，最后调用SendReqStk发送订阅指令

---



#OnRspMarketInfo

当登陆成功后，可以订阅的合约将从此函数推过来。
```cpp
virtual int __cdecl OnRspMarketInfo(struct MarketInfo *pMarketInfo,int bLast)
{
	if (bLast==1)
	{
		return 0;
	}
	
	for(int i=0 ;i<pMarketInfo->stocknum;++i)
	{
		string str_contractInfo  = string(pMarketInfo->stockdata[i].szCode);
		v_contractInfo.push_back(str_contractInfo);
	}

	return 0;
}
```

---
#订阅数量限制、订阅频率限制
- 订阅数量限制：订阅数量是API总共能够订阅合约的总量，由后台行情服务器设置决定
- 行情推送频率：2次/S
- 订阅频率限制：

---

#授权码的获取
授权码与期货公司的行情授权绑定，客户接入实盘时请向期货公司申请授权码

---

# RequestHistory 请求历史行情有限制？

最多12000根，根数从当前时间计查。

---

# 登陆时，返回错误："版本错误，禁止登陆"

这是因为API用的授权码与后台服务器不匹配。

- 模拟环境，用从我们网站或群里下载的Demo里的授权。
- 实盘环境，找期货公司获取授权码。



