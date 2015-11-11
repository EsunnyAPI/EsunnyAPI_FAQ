# 交易

<!-- toc --> 

### 交易接口，为啥会频繁调用OnRtnFund,OnRtnPositionProfit这两个函数?

有持仓的时候，在会发生资金变化和持仓盈亏变化，这两个接口是变化通知接口。

### 为什么OnRtnFill 返回的数据结构里不包含RefString和RefInt？

成交的推送，启明星系统后台主要是考虑到想尽快将成交信息推送给客户，所以没有从委托信息里查找RefString、RefInt。

需要将成交信息与委托信息对应的话，建议在App里处理，OnRtnFill和OnRtnOrder里的信息可以相互映射。

关键字段是： `ServerFlag` + `OrderNo`

---
