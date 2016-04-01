# 交易

# 交易接口，为什么会频繁调用OnRtnFund,OnRtnPositionProfit这两个函数?

有持仓的时候，在会发生资金变化和持仓盈亏变化，这两个接口是变化通知接口。
如果不需要推送，可以在登录时的TapAPITradeLoginAuth中填写相应的NoticeIgnoreFlag。


---

# 为什么OnRtnFill 返回的数据结构里不包含RefString和RefInt？

成交的推送，启明星系统后台主要是考虑到想尽快将成交信息推送给客户，所以没有从委托信息里查找RefString、RefInt。

需要将成交信息与委托信息对应的话，建议在App里处理，OnRtnFill和OnRtnOrder里的信息可以相互映射。

索引字段是： `OrderNo`

---

# 为什么OnRtnOrder中返回的数据结构里撤单量不准确？

由于撤单量取的是交易所返回的撤单量字段，而国内四家交易所并不统一，这个字段有时可能不准确。

建议客户处理的时候判断如果订单状态为已撤单或部分撤单时，按以下方法计算已撤数量：

>  已撤单数量 = 订单总量 - 已成交数量

---
# 升级TapAPI20150320（9.0.1.0）版本API注意事项

从该版本起对下单结构体里的OrderSource字段进行了限制，只能填写TAPI_ORDER_SOURCE_ESUNNY_API，否则会报错-12006（输入错误的TAPIOrderSource），之前的版本没有对此字段限制，升级的时候注意下这个问题。

---

# 关于OnRtnOrder接口的错误码

OnRtnOrder接口返回的错误码分两类：

- 第一类是易盛自己的错误代码，只返回了错误代码，具体错误信息可以参考API文档或头文件
- 第二类是交易所的代码和错误信息

建议的处理逻辑：
![错误码处理](../images/v9_error_code_seq.png)


---
