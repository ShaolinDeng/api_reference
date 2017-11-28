Table of Contents
=================

   * [1 README](#1-readme)
      * [1.1 Official Website](#11-official-website)
      * [1.2 api Online Test Path](#12-api-online-test-path)
      * [1.3 api Online Path](#13-api-online-path)
   * [2 How To Make An API Request From A apikey？](#2-how-to-make-an-api-request-from-a-apikey)
      * [2.1 Apply For apikey](#21-apply-for-apikey)
      * [2.2 Set apikey](#22-set-apikey)
      * [2.3 Use apikey](#23-use-apikey)
   * [3 api List](#3-api-list)
      * [3.1 Formats](#31-formats)
      * [3.2 Query api](#32-query-api)
         * [3.2.1 /v1/user](#321-v1user)
         * [3.2.2 /v1/transfer](#322-v1transfer)
         * [3.2.3 /v1/mdata](#323-v1mdata)
      * [3.3 Trading api](#33-trading-api)
         * [3.3.1 /v1/orderpending](#331-v1orderpending)
      * [3.4 Withdrawal api](#34-withdrawal-api)
         * [3.4.1 /v1/transfer](#341-v1transfer)


# 1 README #

 Bibox exchange api illustration

### 1.1 Official Website ###

https://www.bibox.com/

### 1.2 api Online Test Path ###

https://tapi.bibox.com

### 1.3 api Online Path ###

https://api.bibox.com

# 2 How To Make An API Request From A apikey？ #
### 2.1 Apply For apikey ###

* Log in at https://www.bibox.com/ to operate
* The system generates key and secret, please keep it secretly

### 2.2 Set apikey ###

* Log in at https://www.bibox.com/ to operate
* It is able to set permissions for each apikey

### 2.3 Use apikey ###

* Build request parameters

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
Direction：
	apipath: api request path. Example：https://api.bibox.com/v1/user
	cmds: requested api parameters information.
		cmd: Requesting specific api path parameters，refer to the cmd list below
		body: cmd corresponding parameters，refer to the cmd list below.
	apikey: system generated apikey
	sign: Using secret to md5 convert cmds as sign
```

* Method of generating sign

```
// node.js
	var CryptoJS = require("crypto-js");
	var secret = '7b58254791ada6c0194e6341953f862aff9a91b5';
	var cmds = '[{"cmd":"user/userInfo","body":{}}]';
	var sign = CryptoJS.HmacMD5(cmds, secret).toString();//6a21e39e3f68b6fc2227c4074c7e6a6c
```

# 3 api List #

### 3.1 Formats ###

* Format for request

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

* Return format for request failed

```
{
	error: {
		code: '1000',
		msg: 'something error'
	}
}
```

```
Direction：
	code: error code
	msg: error description
```

* Return format for request succeeded

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
Direction：
	Data in result array correspond with request array cmds，data consistency is not guaranteed.
	each one in the array has a corresponding result，the type of data is corresponded with the is cmd request return type
```

### 3.2 Query api ###

#### 3.2.1 /v1/user ####

* apipath: /v1/user

* cmd list

```
cmd: "user/userInfo"
body: {}
```

#### 3.2.2 /v1/transfer ####

* apipath: /v1/transfer

* cmd list

```
// coin list
cmd: "transfer/coinList"
body: {}
```

```
// obtain deposit address
cmd: "transfer/transferIn"
body: {
    coin_symbol //coin symbol，you can get from transfer/coinList，example：BTC
}
```

```
// obtain available withdrawal address, available withdrawal amount, fees
cmd: "transfer/transferOutInfo"
body: {
    coin_symbol //oin symbol，you can get from transfer/coinList，exmaple：BTC
}
```

```
// obtain deposit list
cmd: "transfer/transferInList"
body: {
    search,  //coin symbol
    filter_type,  //0：all，1：in progress，2：completed，3：failed
    page //page number，start from 1
    size //how many
}
```

```
// obtain withdrawal list
cmd: "transfer/transferOutList"
body: {
    search,  //coin symbol
    filter_type,  //0：all，1：in progress，2：completed，3：failed
    page //page number，start from 1
    size //how many
}
```

```
// My assets
cmd: "transfer/assets"
body: {
    select       //select，1-request all coin assets detals，no-total coin assets
}

Return result
{
    "result":{
        "total_btc":"107.71371658",             //Total assets of all coins，btc denominated
        "total_cny":"5587362.53",               //Total assets of all coins，cny denominated
        "total_usd":"4000.30",                  //Total assets of all coins，usd denominated
        "assets_list":[                         //assets list，no return []
            {
                "coin_symbol":"BTC",
                "balance":"102.31666784",       //balance
                "freeze":"0.01800000",          //Freeze
                "BTCValue":"1100.35043000",     //btc value
                "CNYValue":"59448654.69",       //cny value
                "USDValue":"8945419.86",        //usd value
            },
            {
                "coin_symbol":"ETH",
                "balance":"100.00000000",
                "freeze":"0.00000000",
                "BTCValue":"1100.35043000",     //btc value
                "CNYValue":"59448654.69",       //cny value
                "USDValue":"8945419.86",        //usd value
            },
            {
                "coin_symbol":"LTC",
                "balance":"125.34853787",
                "freeze":"0.00000000",
                "BTCValue":"1100.35043000",     //btc value
                "CNYValue":"59448654.69",       //cny value
                "USDValue":"8945419.86",        //usd value
            },
            {
                "coin_symbol":"BIX",
                "balance":"712.79411732",
                "freeze":"0.00000000",
                "BTCValue":"1100.35043000",     //btc value
                "CNYValue":"59448654.69",       //cny value
                "USDValue":"8945419.86",        //usd value
            }
        ]
    },
    "cmd":"transfer/assets"
}
```

```
//bills
cmd: "transfer/bills"
body: {
    coin_id, //coin id，0:all，otherwise will be obtained from transfer/coinList
    days, // number of days
    type,  // -1：'all', 0：'deposit', 2：'withdrawal', 3：'asks', 4：'fees', 5：'bids', 6：'reward', 7：'invite to get reward'
    page //page number，start from 1
    size //how many
}
```

#### 3.2.3 /v1/mdata ####

* apipath: /v1/mdata

* cmd list

```
//Get k line
cmd: "api/kline"
body: {
    pair,       //pairs，example: BIX_BTC
    period,     //k line period，value ['1min', '3min', '5min', '15min', '30min', '1hour', '2hour', '4hour', '6hour', '12hour', 'day', 'week']
    size,       //how many，1-1000，if not passed will return 1000
    since,      //query start time，example: 1510917300000，if not passed will return last size
}
Return result：
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
//depth
cmd: "api/depth"
body: {
    pair,       //pairs，example: BIX_BTC
    size,       //how many，1-200，if not passed will return 200
}
Return result：
{
    "result":{
        "pair":"LTC_BTC",
        "update_time":1510983122681,
        "asks":[                            //asks depth

        ],
        "bids":[                            //bids depth
            {
                "price":"0.008596",         //price
                "volume":"18.54306495"      //volume
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
//trade history
cmd: "api/deals"
body: {
    pair,       //pairs，example: BIX_BTC
    size,       //how many，1-200，if not passed will return 200
}
Return result：
{
    "result":[
        {
            "id":186973,
            "pair":"LTC_BTC",
            "price":0.008601,                   //transaction price
            "amount":24.12584739,               //transaction amount
            "time":"2017-11-18T05:32:02.000Z",  //transaction time
            "side":2,                           //transaction side，1-bid，2-ask
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
    pair,       //pairs，example: BIX_BTC
}
Return result：
{
    "result":{
        "buy":0,                    //
        "high":0,
        "last":0.008596,            //last transaction price
        "low":0,
        "sell":0,
        "timestamp":1510983122665,  //timestamp
        "vol":70.50811953           //last transaction volume
    },
    "cmd":"api/ticker"
}
```

```
//market data
cmd: "api/market"
body: {
    pair,       //pairs，example: BIX_BTC
}
Return result：
{
    "result":{
        "coin_symbol":"LTC",        //coin type
        "currency_symbol":"BTC",    //currency type
        "last":"0.008601",          //last price today
        "high":"0.31231212",        //highest price today
        "low":"0.00784",            //lowest price today
        "change":"-0.00049900",     //price change today
        "percent":"-5.48%",         //change percentage today
        "vol24H":"11512.53",        //volume for last 24 hours，use the denominated coin as unit
        "last_cny":"82.08",         //last price today converts to cny
        "last_usd":"12.26"          //last price today converts to usd
    },
    "cmd":"api/market"
}
```

### 3.3 Trading api ###

#### 3.3.1 /v1/orderpending ####

* apipath: /v1/orderpending

* cmd list

```
// trade
cmd: "orderpending/trade"
body: {
    pair,           //pairs, BIX_BTC, BIX_ETH, ...
    account_type,   //account type，0-regular account，1-margin account
    order_type,     //order type，1-market order，2-limit order
    order_side,     //order side，1-bid，2-ask
    pay_bix,        //whether using bix for transaction fee，0-no，1-yes
    price,          //price
    amount,         //amount
    money,          //money
}
Return result：
{
    "result": 34,     //return order id
    "cmd":"orderpending/trade"
}

```

```
// cancel trade
cmd: "orderpending/cancelTrade"
body: {
    orders_id,  //order id
}
Return result：
{
    "result":"canceling",
    "cmd":"orderpending/cancelTrade"
}
```

```
// obtain order pending list
cmd: "orderpending/orderPendingList"
body: {
    pair,           //pairs, BIX_BTC, BIX_ETH, ...
    account_type,   //account type，0-regular account，1-margin account
    page,           //page number，start from 1
    size            //how many
}
Return result：
{
    "result":{
        "count":1,
        "page":1,
        "items":[
            {
                "id":159,
                "createdAt":"2017-11-18T06:14:54.000Z",
                "account_type":0,                       //account type 0-regular account，1-margin account
                "coin_symbol":"LTC",                    //coin type
                "currency_symbol":"BTC",                //currency type
                "order_side":2,                         //order side，1-bid，2-ask
                "order_type":2,                         //order type，1-market order，2-limit order
                "price":0.009,                          //price，market order is 0
                "amount":1,                             //amount，market order buy is 0
                "money":0.009,                          //money，market order sell 0
                "deal_amount":0,                        //deal amount
                "deal_percent":"0.00%",                 //deal percentage
                "status":1                              //status，1-pending，2-part completed，3-completed，4-part canceled，5-canceled，6-canceling
            }
        ]
    },
    "cmd":"orderpending/orderPendingList"
}
```

```
// Obtain order history lsist
cmd: "orderpending/orderHistoryList"
body: {
    pair,           //pairs, BIX_BTC, BIX_ETH, ...
    account_type,   //account type，0-regular account，1-margin account
    page,           //page numver，start from 1
    size            //how many
}
Return result：
{
    "result":{
        "count":1,              //record counts
        "page":1,               //current page number
        "items":[               //record details
            {
                "id":228,
                "createdAt":"2017-11-18T06:10:16.000Z",
                "account_type":0,                           //account type 0-regular account，1-margin account
                "coin_symbol":"LTC",                        //coin type
                "currency_symbol":"BTC",                    //currency type
                "order_side":2,                             //order side，1-bid，2-ask
                "order_type":2,                             //order type，1-market order，2-limit order
                "price":"0.00886500",                       //price
                "amount":"1.00000000",                      //amount
                "money":"0.00886500",                       //money，use the denominated coin as unit
                "fee":0                                     //fee
            }
        ]
    },
    "cmd":"orderpending/orderHistoryList"
}
```

### 3.4 Withdrawal api ###

#### 3.4.1 /v1/transfer ####

* apipath: /v1/transfer 

* cmd list

```
// apply for withdrawal
cmd: "transfer/transferOut"
body: {
    totp_code,  // google authentication code
    trade_pwd,  // trading password
    coin_symbol,  // coin symbol，get from transfer/coinList，example：BTC
    amount,  // withdrawal amount
    addr,  // withdrawal address
    addr_remark // address remark
}
```
