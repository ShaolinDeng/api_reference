目录
=================

   * [1 README](#1-readme)
      * [1.1 官网](#11-官网)
      * [1.2 线上api测试路径](#12-线上api测试路径)
      * [1.3 线上api路径](#13-线上api路径)
   * [2 如何使用apikey进行API请求？](#2-如何使用apikey进行api请求)
      * [2.1 申请apikey](#21-申请apikey)
      * [2.2 设置apikey](#22-设置apikey)
      * [2.3 应用apikey](#23-应用apikey)
   * [3 api列表](#4-api列表)
      * [3.1 格式规范](#31-格式规范)
      * [3.2 查询相关api](#32-查询相关api)
         * [3.2.1 /v1/user](#321-v1user)
         * [3.2.2 /v1/transfer](#322-v1transfer)
         * [3.2.3 /v1/mdata](#323-v1mdata)
      * [3.3 交易相关api](#33-交易相关api)
         * [3.3.1 /v1/orderpending](#331-v1orderpending)
      * [3.4 提现相关api](#34-提现相关api)
         * [3.4.1 /v1/transfer](#341-v1transfer)

# 1 README #

 Bibox 交易所 api 相关说明。

### 1.1 官网 ###

https://www.bibox.com/

### 1.2 线上api测试路径 ###

https://tapi.bibox.com

### 1.3 线上api路径 ###

https://api.bibox.com

# 2 如何使用apikey进行API请求？ #
### 2.1 申请apikey ###

* 登陆 https://www.bibox.com/ 进行操作
* 系统会生成 key 和 secret，请秘密保存

### 2.2 设置apikey ###

* 登陆 https://www.bibox.com/ 进行操作
* 可以设置每个 apikey 的权限

### 2.3 应用apikey ###

* 构建请求参数

```
PATH: apipath
METHOD: POST
PARAMS:
{
	"cmds": "[{\"cmd\":\"user/userInfo\",\"body\":{}}]",
	"apikey":"52135958969bedca0809ac10b9caba758022b0a6",
	"sign":"6a21e39e3f68b6fc2227c4074c7e6a6c"
}
```

```
说明：
	apipath: api请求路径。如：https://api.bibox.com/v1/user
	cmds: 请求的 api 参数信息
		cmd: 请求具体的api路径参数，参考下面的 cmd 列表
		body: cmd 对应的参数，参考下面的 cmd 列表
	apikey: 系统生成的 apikey
	sign: 通过 secret 对 cmds 进行 md5 转换后的结果，作为签名
```

* 生成 sign 方法

```
// node.js
	var CryptoJS = require("crypto-js");
	var secret = '7b58254791ada6c0194e6341953f862aff9a91b5';
	var cmds = '[{"cmd":"user/userInfo","body":{}}]';
	var sign = CryptoJS.HmacMD5(cmds, secret).toString();//6a21e39e3f68b6fc2227c4074c7e6a6c
```

# 3 api列表 #

### 3.1 格式规范 ###

* 请求格式

```
{
	cmds: [
		{
			cmd: 'name1',//like user/userInfo
			body: {
				//params1
			}
		},
		{
			cmd: 'name2',//like other
			body: {
				//params2
			}
		}，
		......
	]，
	apikey:"**************",
	sign:"**************"
}
```

* 请求失败返回格式

```
{
	error: {
		code: '1000',
		msg: 'something error'
	}
}
```

```
说明：
	code: 错误码
	msg: 错误描述
```

* 请求成功返回格式

```
{
	result: [
		{
			cmd: 'name2',//like user/userInfo
			result: result2 //any type data
		},
		{
			cmd: 'name1',//like user/userInfo
			result: result1 //any type data
		},
		......
	]
}
```

```
说明：
	result 数组里的数据对应请求的数组 cmds，不保证顺序一致性
	数组中的每一项单独有对应的 result，数据类型是对应 cmd 请求的返回类型
```

### 3.2 查询相关api ###

#### 3.2.1 /v1/user ####

* apipath: /v1/user

* cmd 列表

```
cmd: "user/userInfo"
body: {}
```

#### 3.2.2 /v1/transfer ####

* apipath: /v1/transfer

* cmd 列表

```
// 币种列表
cmd: "transfer/coinList"
body: {}
```

```
// 获取充值地址
cmd: "transfer/transferIn"
body: {
    coin_symbol //币种符号，可从 transfer/coinList 中获得，如：BTC
}
```

```
// 查询可用提现地址，可提现金额，手续费
cmd: "transfer/transferOutInfo"
body: {
    coin_symbol //币种符号，可从 transfer/coinList 中获得，如：BTC
}
```

```
// 获取充值列表
cmd: "transfer/transferInList"
body: {
    search,  //币种符号
    filter_type,  //0：全部，1：进行中，2：已完成，3：失败
    page //第几页，从1开始
    size //要几条
}
```

```
// 获取提现列表
cmd: "transfer/transferOutList"
body: {
    search,  //币种符号
    filter_type,  //0：全部，1：进行中，2：已完成，3：失败
    page //第几页，从1开始
    size //要几条
}
```

```
// 我的资产
cmd: "transfer/assets"
body: {
    select       //可选，1-请求所有币种资产明细，不传-各币种总资产合计
}

返回结果
{
    "result":{
        "total_btc":"107.71371658",             //各币种资产总和，btc计价
        "total_cny":"5587362.53",               //各币种资产总和，cny计价
        "total_usd":"4000.30",                  //各币种资产总和，usd计价
        "assets_list":[                         //币种资产明细，没有返回 []
            {
                "coin_symbol":"BTC",
                "balance":"102.31666784",       //可用
                "freeze":"0.01800000",          //冻结
                "BTCValue":"1100.35043000",     //btc估值
                "CNYValue":"59448654.69",       //cny估值
                "USDValue":"8945419.86",        //usd估值
            },
            {
                "coin_symbol":"ETH",
                "balance":"100.00000000",
                "freeze":"0.00000000",
                "BTCValue":"1100.35043000",     //btc估值
                "CNYValue":"59448654.69",       //cny估值
                "USDValue":"8945419.86",        //usd估值
            },
            {
                "coin_symbol":"LTC",
                "balance":"125.34853787",
                "freeze":"0.00000000",
                "BTCValue":"1100.35043000",     //btc估值
                "CNYValue":"59448654.69",       //cny估值
                "USDValue":"8945419.86",        //usd估值
            },
            {
                "coin_symbol":"BIX",
                "balance":"712.79411732",
                "freeze":"0.00000000",
                "BTCValue":"1100.35043000",     //btc估值
                "CNYValue":"59448654.69",       //cny估值
                "USDValue":"8945419.86",        //usd估值
            }
        ]
    },
    "cmd":"transfer/assets"
}
```

```
//用户账单
cmd: "transfer/bills"
body: {
    coin_id, //币种id，0:全部，否则从 transfer/coinList 中获得
    days, // 最近多少天的
    type,  // -1：'全部', 0：'充值', 2：'提現', 3：'賣出', 4：'手續費', 5：'買入', 6：'系统奖励', 7：'推荐返现'
    page //第几页，从1开始
    size //要几条
}
```

#### 3.2.3 /v1/mdata ####

* apipath: /v1/mdata

* cmd 列表

```
//获取k线
cmd: "api/kline"
body: {
    pair,       //交易对，如BIX_BTC
    period,     //k线周期，取值 ['1min', '3min', '5min', '15min', '30min', '1hour', '2hour', '4hour', '6hour', '12hour', 'day', 'week']
    size,       //要几条，1-1000，不传返回1000
}
返回结果：
{
    "result":[
        {
            "id":28288,
            "period":"1min",
            "pair":"LTC_BTC",
            "open":0.00711,
            "high":0.00711,
            "low":0.00711,
            "close":0.00711,
            "vol":0,
            "createdAt":"2017-11-17T02:15:00.000Z"
        },
        {
            "id":28295,
            "period":"1min",
            "pair":"LTC_BTC",
            "open":0.00711,
            "high":0.00711,
            "low":0.00711,
            "close":0.00711,
            "vol":0,
            "createdAt":"2017-11-17T02:16:00.000Z"
        }
    ],
    "cmd":"api/kline"
}
```

```
//深度
cmd: "api/depth"
body: {
    pair,       //交易对，如BIX_BTC
    size,       //要几条，1-200，不传返回200
}
返回结果：
{
    "result":{
        "pair":"LTC_BTC",
        "update_time":1510983122681,
        "asks":[                            //卖方深度

        ],
        "bids":[                            //买方深度
            {
                "price":"0.008596",         //挂单价格
                "volume":"18.54306495"      //挂单数量
            },
            {
                "price":"0.00859289",
                "volume":"40.13567123"
            }
        ]
    },
    "cmd":"api/depth"
}
```

```
//历史成交
cmd: "api/deals"
body: {
    pair,       //交易对，如BIX_BTC
    size,       //要几条，1-200，不传返回200
}
返回结果：
{
    "result":[
        {
            "id":186973,
            "pair":"LTC_BTC",
            "price":0.008601,                   //成交价格
            "amount":24.12584739,               //成交数量
            "time":"2017-11-18T05:32:02.000Z",  //成交时间
            "side":2,                           //交易方向，1-买，2-卖
            "createdAt":"2017-11-18T05:32:02.000Z",
            "updatedAt":"2017-11-18T05:32:02.000Z"
        },
        {
            "id":186974,
            "pair":"LTC_BTC",
            "price":0.008596,
            "amount":70.50811953,
            "time":"2017-11-18T05:32:02.000Z",
            "side":2,
            "createdAt":"2017-11-18T05:32:02.000Z",
            "updatedAt":"2017-11-18T05:32:02.000Z"
        }
    ],
    "cmd":"api/deals"
}
```

```
//ticker
cmd: "api/ticker"
body: {
    pair,       //交易对，如BIX_BTC
}
返回结果：
{
    "result":{
        "buy":0,                    //
        "high":0,
        "last":0.008596,            //最新成交价
        "low":0,
        "sell":0,
        "timestamp":1510983122665,  //时间戳
        "vol":70.50811953           //最新成交量
    },
    "cmd":"api/ticker"
}
```

```
//市场行情数据
cmd: "api/market"
body: {
    pair,       //交易对，如BIX_BTC
}
返回结果：
{
    "result":{
        "id":4,
        "coin_symbol":"BIX",            //交易币种
        "currency_symbol":"BTC",        //定价币种
        "last":"0.00002704",            //最新价
        "high":"0.00003480",
        "low":"0.00001951",
        "change":"+0.00000715",         //24h涨跌
        "percent":"+35.95%",            //24h涨跌幅
        "vol24H":"641954",              //24h成交量
        "amount":"16.55",               //24h成交额
        "last_cny":"1.84",              //最新价折算cny
        "high_cny":"2.37",
        "low_cny":"1.33",
        "last_usd":"0.27",              //最新价折算usd
        "high_usd":"0.35",
        "low_usd":"0.19"
    },
    "cmd":"api/market"
}
```

```
//全币种市场行情数据
cmd: "api/marketAll"
body: {
}
返回结果：
{
    "result":[
        {
            "id":4,
            "coin_symbol":"BIX",
            "currency_symbol":"BTC",
            "last":"0.00002704",
            "high":"0.00003480",
            "low":"0.00001951",
            "change":"+0.00000715",
            "percent":"+35.95%",
            "vol24H":"641954",
            "amount":"16.55",
            "last_cny":"1.84",
            "high_cny":"2.37",
            "low_cny":"1.33",
            "last_usd":"0.27",
            "high_usd":"0.35",
            "low_usd":"0.19"
        },
        {
            "id":5,
            "coin_symbol":"ETH",
            "currency_symbol":"BTC",
            "last":"0.04341296",
            "high":"0.04884176",
            "low":"0.04321405",
            "change":"-0.00047878",
            "percent":"-1.09%",
            "vol24H":"86",
            "amount":"3.77",
            "last_cny":"2950.87",
            "high_cny":"3319.88",
            "low_cny":"2937.35",
            "last_usd":"432.60",
            "high_usd":"486.70",
            "low_usd":"430.62"
        }
    ],
    "cmd":"api/marketAll"
}
```

### 3.3 交易相关api ###

#### 3.3.1 /v1/orderpending ####

* apipath: /v1/orderpending

* cmd 列表

```
// 下单
cmd: "orderpending/trade"
body: {
    pair,           //交易对, BIX_BTC, BIX_ETH, ...
    account_type,   //账户类型，0-普通账户，1-信用账户
    order_type,     //交易类型，1-市价单，2-限价单
    order_side,     //交易方向，1-买，2-卖
    pay_bix,        //是否bix抵扣手续费，0-不抵扣，1-抵扣
    price,          //委托价格
    amount,         //委托数量
    money,          //委托金额
}
返回结果：
{
    "result": 34,     //返回委托单id
    "cmd":"orderpending/trade"
}

```

```
// 撤销订单
cmd: "orderpending/cancelTrade"
body: {
    orders_id,  //委托单id
}
返回结果：
{
    "result":"撤销中",
    "cmd":"orderpending/cancelTrade"
}
```

```
// 获取用户订单列表
cmd: "orderpending/orderPendingList"
body: {
    pair,           //交易对, BIX_BTC, BIX_ETH, ...
    account_type,   //账户类型，0-普通账户，1-信用账户
    page,           //第几页，从1开始
    size            //要几条
}
返回结果：
{
    "result":{
        "count":1,
        "page":1,
        "items":[
            {
                "id":159,
                "createdAt":"2017-11-18T06:14:54.000Z",
                "account_type":0,                       //账户类型 0-普通账户，1-信用账户
                "coin_symbol":"LTC",                    //交易币种
                "currency_symbol":"BTC",                //定价币种
                "order_side":2,                         //交易方向，1-买，2-卖
                "order_type":2,                         //订单类型，1-市价单，2-限价单
                "price":0.009,                          //委托价格，市价单是0
                "amount":1,                             //委托数量，市价买单是0
                "money":0.009,                          //委托金额，市价卖单是0
                "deal_amount":0,                        //已成交数量
                "deal_percent":"0.00%",                 //成交百分比
                "status":1                              //状态，1-待成交，2-部分成交，3-完全成交，4-部分撤销，5-完全撤销，6-待撤销
            }
        ]
    },
    "cmd":"orderpending/orderPendingList"
}
```

```
// 获取用户成交单列表
cmd: "orderpending/orderHistoryList"
body: {
    pair,           //交易对, BIX_BTC, BIX_ETH, ...
    account_type,   //账户类型，0-普通账户，1-信用账户
    page,           //第几页，从1开始
    size            //要几条
}
返回结果：
{
    "result":{
        "count":1,              //记录总数
        "page":1,               //当前第几页
        "items":[               //记录明细
            {
                "id":228,
                "createdAt":"2017-11-18T06:10:16.000Z",
                "account_type":0,                           //账户类型 0-普通账户，1-信用账户
                "coin_symbol":"LTC",                        //交易币种
                "currency_symbol":"BTC",                    //定价币种
                "order_side":2,                             //交易方向，1-买，2-卖
                "order_type":2,                             //订单类型，1-市价单，2-限价单
                "price":"0.00886500",                       //成交价格
                "amount":"1.00000000",                      //成交量
                "money":"0.00886500",                       //成交额，单位是定价币种
                "fee":0                                     //手续费
            }
        ]
    },
    "cmd":"orderpending/orderHistoryList"
}
```

### 3.4 提现相关api ###

#### 3.4.1 /v1/transfer ####

* apipath: /v1/transfer 

* cmd 列表

```
// 申请提现
cmd: "transfer/transferOut"
body: {
    totp_code,  // google 验证码
    trade_pwd,  // 交易密码
    coin_symbol,  // 币种符号，可从 transfer/coinList 中获得，如：BTC
    amount,  // 提币数量
    addr,  // 提现地址
    addr_remark // 地址别名
}
```
