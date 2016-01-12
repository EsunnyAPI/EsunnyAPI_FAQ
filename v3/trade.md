# 3.0交易 常见问题
# 3.0交易



---


# 应答与推送

- 应答：API发出请求，对API请求的回应，应答以OnRsp开头，例如发出OrderInsert下单指令，OnRspOrderInsert为对下单指令的应答。一问一答。

- 推送：由服务器主动推送给API，以OnRtn开头，例如客户委托状态的变更，排队、部分成交、完全成交、撤单等。



---


# 成交推送与委托状态推送

- 单腿成交信息推送
  
```cpp
virtual void __cdecl OnRtnMatchState(const TEsMatchStateNoticeField& rsp) = 0
```
- 多腿成交信息推送

```cpp
virtual void __cdecl OnRtnMatchInfo(const TEsMatchInfoNoticeField& rsp) = 0
```
   
成交信息的推送，具体再哪个函数返回取决于易盛的网关程序，多腿成交返回与OnRtnMatchInfo，单腿成交返回于OnRtnMatchState或者OnRtnMatchInfo，开发者在开发时需要处理以上两个函数接收成交。

- 委托信息状态变化推送

```cpp
virtual void __cdecl OnRtnOrderState(const TEsOrderStateNoticeField& rsp) = 0
```

- 下单推送，非自身API下单，由其他终端如itrader3.0  极星客户端下单的推送

```cpp
virtual void __cdecl OnRtnOrderInfo(const TEsOrderInfoNoticeField& rsp) = 0
```



---
# 错误码
API中错误码：
- 发送API指令返回错误码，说明API指令未发送成功


```cpp
int  ret = Tap_Api->Login(req,iReqID);
ret==11
//未连接或者网络错误
const TErrorCodeType Err_Network_Disconnected  =11; 
```
        
- 由OnRsp*** OnRtn***返回的错误码，由后台服务返回


```cpp
OnLogin(const TEsLoginRspField* rsp, int errCode, const int iReqID）

errCode ==7;

//登录密码错

const TErrorCodeType Err_Login_Password= 7;
```

关于错误码的解释在EsForeignApiErrCode.h定义，开发者可以根据定义查找错误原因。



---

# 下单频率


通用授权下单频率为10笔/S，如有特殊需求可再申请


---


#TEsOrderInsertReqField报单请求结构

|字段类型| 字段名称| 中文解释| 是否必填|
| --------| -----  | --  | --  |
|TClientNoType|ClientNo|客户号|必填|
|TCommodityNoType|CommodityNo|商品代码|必填|
|TContractNoType|ContractNo|合约代码|必填|
|TOrderTypeType|OrderType|委托类型|必填|
|TOrderWayType|OrderWay|委托方式,用于标识订单委托来源|必填|
|TOrderModeType|OrderMode|委托模式|必填|
|TDateTimeType|ValidDateTime|有效日期(GTD情况下使用)|GTD必填|
|TIsRiskOrderType|IsRiskOrder|风险报单|非必填，个人客户登陆身份，风险报单为否|
|TDirectType|Direct|买入卖出|必填|
|TOffsetType|Offset|开仓平仓|必填|
|THedgeType|Hedge|投机保值|非必填|
|TTradePriceType|OrderPrice|委托价格|必填|
|TTradePriceType|TriggerPrice|触发价格|止损单必填|
|TTradeVolType|OrderVol|委托数量|必填|
|TTradeVolType|MinMatchVol|最小成交量|非必填|


一个限价单的报单结构示例：

```cpp
    TEsOrderInsertReqField req;
	memset(&req,0,sizeof(req));
	//客户号
	strcpy_s(req.ClientNo,"001");
	//商品代码
	strcpy_s(req.CommodityNo,"CN");
	//合约代码
	strcpy_s(req.ContractNo,"1509");
	//买卖方向
	req.Direct=DIRECT_BUY;
	//开仓平仓
	req.Offset=OFFSET_OPEN;
	//委托类型
	req.OrderType=ORDER_TYPE_LIMIT;
	//委托模式
	req.OrderMode=ORDER_MODE_GFD;
	//委托价格
	req.OrderPrice=630;
	//委托数量
	req.OrderVol=1;
```	

---

#OnLogin与OnInitFinished
发送Login成功后，收到OnLogin应答成功后收到初始化操作完成，所有的业务操作需要OnInitFinished本响应errCode为0（成功）后可进行

---

#如何获取交易的合约代码
1. QryCommodity 查询所有交易品种
2. QryContract 根据第一步获取到的品种，查询品种对应的交易合约
---

#请求ID iReqID


常见应用场景：API开发者发出指令，在应答中如何知道此次为对开发者刚才发出指令的应答


例如：报单指令
```
int iReqID ; 

调用OrderInsert( req,  iReqID) ; 后返回iReqID=10;

在报单指令应答中

OnRspOrderInsert( rsp, errCode, iReqID)

iReqID ==10 对应刚才发出的下单指令
```
- iReqID  由AP自身维护，当指令发成功则自增1 。如果判断指令发送成功？请参考
  ‘错误码’说明

#成交流号 、 成交ID 、 成交编号
- 成交流号:（易盛系统生成） 成交ID （易盛系统生成） 成交编号 （上手生成）
- 成交ID：一笔成交生成一个成交ID，当成交状态变化后，成交ID不改变
- 成交流号：一笔成交生成一个成交ID，当成交状态变化后，成交流号也重新生成

---
#513 Err_Frnt_APPNotAllow 客户应用没有授权问题排查
  
```cpp
//登录请求结构
struct TEsLoginReqField
{    
    TIsCaLoginType				IsCaLogin;              //是否CA认证
    TEsLoginIdentityType		Identity;               //登录身份类型,目前只支持单客户
    TIsForcePasswordType		IsForcePwd;             //是否强制修改密码
    /// 使用不同账户登录时共用登录号
    union														
    {
        TClientNoType				ClientNo;			//客户号,代理客户号
        TOperatorNoType				OperatorNo;			//操作员号,代理操作员号
    };
    /// 对应所使用的登录账号的密码
    TLoginPasswordType			LoginPwd;				//登录密码
    /// 强制修改密码时的新密码
    TLoginPasswordType			NewPwd;					//强制修改密码登录时,新修改密码
    TOtpPassType				OtpPass;                //otp认证密码
    TEsSizeType                 CaLen;                  //CA信息长度，IsCaLogin为'Y'时，本字段有效
    TCaInfoType					CaInfo;					//CA登录时填写,IsCaLogin为'Y'时，本字段有效
};
```
    
    
- 检查授码是否正确，连接地址是否正确
- 登陆身份，IDENTITY_CLIENT（个人客户）、 IDENTITY_TRADER（交易员）、 IDENTITY_MANAGE（管理员），后台配置为何种类型授权，用相应身份登陆
- 结构体填充时，个人客户用ClientNo，交易员操作员用 OperatorNo。
- 联系IT，检查后台API授权设置，核对授权是否与后台设置一致。

---
#1 Err_Login_Version 登录版本不允许

- 查看授权pdf文档，授权类型如果为基础性检查，则为3.2.7系统专用，授权类型为完整性检查则为3.2.7以上系统专用


---

#币种与汇率

```cpp
//币种查询应答结构
struct TEsCurrencyQryRspField
{
    TCurrencyNoType				CurrencyNo;
    TCurrencyNameType			CurrencyName;
    TIsPrimaryCurrencyType		IsPrimary;
    TCurrencyGroupFlagType		CurrencyGroup;
    TExchangeRateType			ExchangeRate;
};
```
    

- IsPrimar 是否基币
- CurrencyGroup币种组标志(同一币种组，资金共享）
- ExchangeRate汇率

- 汇率计算示例:

| HKD |IsPrimary=Y | CurrencyGroup=A|ExchangeRate=1.0 |
| -- | -- | -- | -- |
| USD| IsPrimary=N | CurrencyGroup=A |ExchangeRate=7.749|

---


#港交所夜盘交易

- 实盘：交易港交所夜盘时需要加入 港交所交易时段后有效标记，否则无法交易。
报单请求结构中Hedge = HEDGE_B即可。

- 模拟：模拟系统未模拟此交易规则，无论是否填写HEDGE_B，均可在晚间交易

---
#API 申请流程

![API申请流程](../images/v3_api_shen_qing_liu_cheng.png)

---

#客户自定义保留字段

- 在报单结构中提供了客户保留字段，客户可以填充字段，用于标识此定单由您的开发程中发出。

- 客户保留字段不会显示于易盛公司的itrade、极星客户端上，但是会保存在数据库中




    //报单请求结构
    struct TEsOrderInsertReqField
    {
        ...
        ...
        TOrderSaveIntType			SaveInt;                //客户保留字段1
        TOrderSaveDoubleType		SaveDouble;             //客户保留字段2
        TOrderSaveStringType		SaveString;             //客户保留字段3
    };
    

#断线重连

- 断线后onclose回调通知
- 不要在onclose回调中进行open重连操作，否则将造成阻塞open函数无回应。
- 

---
#港交所做市商报单HKMarketOrderOperator

-  两个指令可以任意组合，下单|下单   下单|撤单     撤单|改单  改单|下单   下单|改单
-  系统中显示的是两笔委托，没有区分标记此委托是做市商单，所以开发者需要自己处理
-  做市商指令 是港交所指令，网关直接将做市商指令发送给港交所


---
#CreateEsunnyForeignTradeApi 错误码

其中高16位为保留信息，低16位为错误码，获取实际错误码需要对高16位清零。

例如 pRetCode=66136.
0000 0000 0000 0001 0000 0010 0101 1000
高16位清零
0000 0000 0000 0000 0000 0010 0101 1000=600


