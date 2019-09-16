# REST行情与交易接口 V2

## 基本信息
* **请求域名** [https://api.coinegg.vip](https://api.coinegg.vip)
* **请求过程说明**
	* 构造请求数据，用户数据按照COINEGG提供的接口规则，通过程序生成签名和要传输给CoinEgg的数据集合；
	* 发送请求数据，把构造完成的数据集合通过POST/GET提交的方式传递给CoinEgg；
	* CoinEgg对请求数据进行处理，服务器在接收到请求后，会首先进行安全校验，验证通过后便会处理该次发送过来的请求；
	* 返回响应结果数据，CoinEgg把响应结果以JSON的格式反馈给用户，具体的响应格式，错误代码参见接口部分；
	* 对获取的返回结果数据进行处理；
* **安全认证**
	* 所有的private API都需要经过认证
	* Api的申请可以到财务中心 -> API，申请得到私钥和公钥，私钥CoinEgg将不做储存，一旦丢失将无法找回
	
	> ####**注意:请勿向任何人泄露这两个参数，这像您的密码一样重要**
	
* **签名机制**
	*	 每次请求private api 都需要验证签名，发送的参数示例如下
	
	```
	$param = ['amount' => 1,  
				'price' => 10000,    
				'type' => 'buy', 
				'nonce' => 141377098123,
				'key' => '5zi7w-4mnes-swmc4-egg9b-f2iqw-396z4-g541b',
				'signature' => '459c69d25c496765191582d9611028b9974830e9dfafd762854669809290ed82'
            ];
    ```
    
	```
	1. key 是申请到的公钥
	2. signature是签名，是将amount price type nonce key等各个请求参数以key=value的形式使用'&'符将各个参数拼接起来(字符串结尾处不可有'&'),通过md5(私钥)为key,拼接字符串为值的形式进行sha256算法加密得到的值
	3. （重要）签名的字段顺序要与提交的字段顺序务必保持一致，例如：生成签名的字段顺序为['a'=>'a','b'=>'b','c'=>'c']，在提交请求的时间也应该保证参数的顺序为['a'=>'a','b'=>'b','c'=>'c']
    ```

## 公开API接口

### 1.获取币对信息
#### HTTP请求
```
GET /api/v2/symbols
```
#### 返回参数

参数名	|类型	|描述
------------- | ------------- |------------- 
base_currency |	String|	交易货币币种
quote_currency	|String	|计价货币币种
symbol	|String	|币对名称
min_size	|String	|最小交易金额
size_increment	|String	|	交易货币数量精度
tick_size	|String	|	交易货币价格精度
#### 返回示例
```
[
  {
        "base_currency":"eth",
        "quote_currency":"usdt",
        "symbol":"eth_usdt",
        "min_size":"5.0000000000",
        "size_increment":"0.0001",
        "tick_size":"0.00000001"
    },
    {
        "base_currency":"btc",
        "quote_currency":"usdt",
        "symbol":"btc_usdt",
        "min_size":"5.0000000000",
        "size_increment":"0.0001",
        "tick_size":"0.00000001"
    },
    {
        "base_currency":"eth",
        "quote_currency":"btc",
        "symbol":"eth_btc",
        "min_size":"0.0010000000",
        "size_increment":"0.0001",
        "tick_size":"0.00000001"
    },
     {
        "base_currency":"xrp",
        "quote_currency":"btc",
        "symbol":"xrp_btc",
        "min_size":"0.0010000000",
        "size_increment":"0.0001",
        "tick_size":"0.0000000001"
    }
]
```
#### 解释说明
```

min_size指下单金额的最小值（例如5 USDT 或者 0.001BTC）。size_increment(交易数量步长)是指下单数量的最小
增量。例如，当size_increment为0.000001，委托数量传入0.0000126将被系统按截尾法修正为
0.000012。

tick_size(价格步长)是指下单价格的最小增量，委托价格必须是tick_size的倍数。例如，当
tick_size为0.0001，委托价格传入0.02237将被系统按截尾法修正为0.0223。
```

### 2.获取全部ticker信息 
#### HTTP请求
```
GET /api/v2/ticker
```
#### 返回参数

参数名	|类型	|描述
------------- | ------------- |------------- 
symbol	|String|	交易对信息
high	|String	|24小时最高价
low	|String	|24小时最低价
last	|String|	最新成交价
bid	|String	|买一价
ask	|String	|卖一价
volume|	String	|24小时成交量
amount|	String|	24小时成交额
timestamp	|Int	|时间戳
#### 返回示例
```
[
    {
        "symbol": "bch_btc",
        "high": "0",
        "low": "0",
        "last": "0",
        "bid": "0.05150701",
        "ask": "0.05489199",
        "volume": "0",
        "amount": "0",
        "timestamp": 1558089930184
    },
    {
        "symbol": "eth_btc",
        "high": "0",
        "low": "0",
        "last": "0",
        "bid": "0.02985072",
        "ask": "0.03009662",
        "volume": "0",
        "amount": "0",
        "timestamp": 1558089930184
    },
    {
        "symbol": "etc_btc",
        "high": "0",
        "low": "0",
        "last": "0",
        "bid": "0.001013",
        "ask": "0.00103501",
        "volume": "0",
        "amount": "0",
        "timestamp": 1558089930184
    }
]
   
```
### 3.获取单个ticker信息
#### HTTP请求
```
GET /api/v2/ticker/symbol/<symbol>
```
#### 请求参数

参数名	|类型	|是否必填|描述
------------- | -------------  | ------------- |------------- 
symbol	|String|	是|交易对信息  例: bch_btc

#### 请求实例
```
GET /api/v2/ticker/symbol/bch_btc
```

#### 返回参数

参数名	|类型	|描述
------------- | ------------- |------------- 
symbol	|String|	交易对信息
high	|String	|24小时最高价
low	|String	|24小时最低价
last	|String|	最新成交价
bid	|String	|买一价
ask	|String	|卖一价
volume|	String	|24小时成交量
amount|	String|	24小时成交额
timestamp	|Int	|时间戳
#### 返回示例
```
[
    {
        "symbol": "bch_btc",
        "high": "0",
        "low": "0",
        "last": "0",
        "bid": "0.05150701",
        "ask": "0.05489199",
        "volume": "0",
        "amount": "0",
        "timestamp": 1558090464191
    }
]
   
```
### 4.获取深度数据 
#### HTTP请求
```
GET /api/v2/depth/symbol/<symbol>
```
#### 请求参数

参数名	|类型	|是否必填|描述
------------- | ------------- |------------- | -------------
symbol	|String|	是| 交易对信息  例: bch_btc

#### 请求实例
```
GET /api/v2/depth/symbol/bch_btc

```
#### 返回参数

参数名	|类型	|描述
------------- | ------------- |------------- 
价格	|String	|深度价格
数量	|String	|深度数量
#### 返回示例
```
    {
    "asks": [
        [
            "0.0548919900",
            "2.9222000000"
        ],
        [
            "0.0554760000",
            "10.0000000000"
        ],
        [
            "0.0563100000",
            "10.0000000000"
        ]
    ],
    "bids": [
        [
            "0.0515070100",
            "0.1200000000"
        ],
        [
            "0.0460000000",
            "0.5515000000"
        ],
        [
            "0.0360000000",
            "2.0406000000"
        ]
    ]
}
   
```
#### 解释说明
```
asks ： 卖方深度
bids ： 买方深度

```
### 5.获取成交数据 
#### HTTP请求
```
GET /api/v2/trades/symbol/<symbol>
```
#### 请求参数
参数名	|类型	|是否必填|描述
------------- | ------------- |------------- |------------- 
symbol	|String	|是|交易对信息  例: bch_btc
from	|String	|否|请求此成交id之后的内容 
#### 请求示例
```
/api/v2/trades/symbol/bch_btc?from=1868690
```
#### 返回参数

参数名	|类型	|描述
------------- | ------------- |------------- 
timestamp	|String|	成交时间
price	|String	|成交价格
size	|String	|成交数量
trade_id|	String|	成交ID
side	|String	|成交方向
#### 返回示例
```
[
    {
        "timestamp": "1556222959",
        "price": "0.053514",
        "size": "13.03",
        "trade_id": "1868693",
        "side": "sell"
    },
    {
        "timestamp": "1556222942",
        "price": "0.05243",
        "size": "0.2397",
        "trade_id": "1868692",
        "side": "sell"
    },
    {
        "timestamp": "1556222942",
        "price": "0.053435",
        "size": "3.078",
        "trade_id": "1868691",
        "side": "sell"
    }
]
   
```
#### 解释说明
```
成交方向side是指Taker订单的下单方向,Taker表示主动与深度列表中挂单进行成交。
buy表示价格上涨，因为Taker是买单吃掉深度，所以价格将上行。相反，sell表示价格下跌。
```
### 6.获取K线数据 
#### HTTP请求
```
GET /api/v2/kline/symbol/<symbol>/period/<period>?from=<from>&to=<to>
```
#### 请求实例
```
GET /api/v2/kline/symbol/btc_usdt/period/5?from=0&to=345600

```
#### 请求参数

参数名	|类型	|是否必填|描述
------------- | ------------- |------------- |------------- 
symbol	|String	|是|	交易对名称
period | String	|是|	时间粒度
from	|String	|否	|开始时间戳
to	|String	|否	|结束时间戳
#### 返回参数

```
[
    [
        "时间戳",
        "开",
        "高",
        "低",
        "收",
        "成交量"
    ]
]
```
#### 返回示例
```
[
    [
        "1530374400",
        "449.9361000000",
        "450.6376000000",
        "449.5974000000",
        "450.6376000000",
        "5.5848000000"
    ]
]
   
```
#### 解释说明
```
如果用户没有提供开始时间或结束时间中的任一字段，则两个字段都将被忽略。

未提供开始时间和结束时间的请求，则系统按时间粒度返回最近的数据。

时间粒度period 必须是[5 15 30 60 240 86400]中的任一值，否则请求将被拒绝。

这些值分别对应的是[5min 15min 30min 1hour 2hour 4hour 1day]的时间段。

K线数据可能不完整。K线数据不应该轮询调用。

如果from 和 to 不传值，默认会从当前时间来计算请求。

```
## 私有API接口
### 1.币币交易全部币种账户信息
#### 解释说明
```
暂只有币币交易资产查询，其他资产后续开放。
```
#### HTTP请求
```
POST /api/v2/accounts
```
#### 请求参数
参数名	|类型	|是否必填|描述
------------- | ------------- |------------- |------------- 
key	|String|	是|	API key
signature	|String	|是|	签名

#### 返回参数

参数名	|类型	|描述
------------- | ------------- |------------- 
currency|	String	|币种
balance	|String	|余额
hold|	String	|冻结(不可用)
available	|String	|可用于交易的数量
#### 返回示例
```
[
    {
        "currency":"btc",
        "balance":"0",
        "available":"0.00000002",
        "hold":"0"
    },
    {
        "currency":"eth",
        "balance":"0.00000012",
        "available":"0",
        "hold":"0.00000012"
    },
    {
        "currency":"ubtc",
        "balance":"0.00000003",
        "available":"0",
        "hold":"0.00000003"
    },
    {
        "currency":"usdt",
        "balance":"40.00123847",
        "available":"0",
        "hold":"40.00123847"
    },
    {
        "currency":"xas",
        "balance":"0.000334",
        "available":"0",
        "hold":"0.000334"
    }
]
   
```
### 2.币币交易单一币种账户信息

#### HTTP请求
```
POST /api/v2/accounts/currency/<currency>
```
#### 解释说明
```
暂只有币币交易资产查询，其他资产后续开放。
```
#### 请求参数
参数名	|类型	|是否必填|描述
------------- | ------------- |------------- |------------- 
currency	|String	|是	|币种名称 [必填] 例: btc
key	|String	|是	|API key
signature|	String	|是|	签名

#### 请求示例
```
/api/v2/accounts/currency/btc
```
#### 返回参数

参数名	|类型	|描述
------------- | ------------- |------------- 
currency|	String	|币种
balance|	String	|余额
hold	|String	|冻结(不可用)
available|	String	|可用于交易的数量
#### 返回示例
```
[
    {
        "currency":"btc",
        "balance":"0",
        "available":"0.00000002",
        "hold":"0"
    }
]
   
```	
### 3.下单 
#### HTTP请求
```
POST /api/v2/orders/
```
#### 请求参数
参数名	|类型	|是否必填|描述
------------- | ------------- |------------- |------------- 
key	|String	|是	|API key
signature	|String|	是|	签名
symbol	|String	|是	|交易对名称 列：bch_btc
side	|String	|是|	buy or sell
margin_trading|	String	|否	|下单类型(当前为币币交易，请求值为1)
#### 限价单特殊参数
参数名	|类型	|是否必填|描述
------------- | ------------- |------------- |------------- 
price|	String|	是|	价格
size	|String	|是|	买入或卖出的数量
#### 返回参数

参数名	|类型	|描述
------------- | ------------- |------------- 
result	|Boolean|	下单结果。若是下单失败，将给出错误码提示
order_id|	String	|订单ID
#### 返回示例
```
{
    "result":true,
    "order_id":"144670619"
}
   
```
#### 解释说明
```
symbol : symbol必须是有效的币对。币对列表可通过/ symbols接口获取。

type : 下单时，您可以指定订单类型。您指定的订单类型将决定是否需要其他订单参数以及撮合引擎如何执
行您的订单。如果type没有指定，订单类型将默认为limit。限价单既是默认订单类型，也是基本订单类
型。限价单需要指定price和size。size是买入或卖出交易货币的数量，price表示每个交易货币相对计价
货币的价格。限价单会按指定价格或更好的价格成交。根据市场条件，卖单可以按照指定价格或者更高价格来
成交，买单可以按照指定价格或更低价格成交。如果限价单不能立即成交，那么限价单将进入深度列表，直到
被另一个新订单成交或被用户撤单。

price : price表示买入或卖出的价格。

size : size表示买入或卖出交易货币的数量。

hold : 对于限价买单，系统将冻结计价货币的数量 = 限定价格 x 买入数量。对于限价卖单，系统将冻结
你想卖出的交易货币的数量。如果您取消部分成交或未成交的订单，剩余资金将被解除冻结。
   
```
### 6.批量下单
#### HTTP请求
```
POST /api/v2/batchOrders/
```
#### 请求参数
参数名	|类型	|是否必填|描述
------------- | ------------- |------------- |------------- 
key	|String|	是	|API key
signature	|String	|是|	签名
orders|	List	|是	|下单列表
symbol|	String	|是	|交易对名称 列：bch_btc
side	|String	|是	|buy or sell
#### 请求示例
```
/api/v2/cancelOrders
{
"orders":"[
    {"symbol":"bch_btc","side":"buy","price":"0.0121","size":"51"},
    {"symbol":"bch_btc","side":"buy","price":"0.0121","size":"52"},
    {"symbol":"bch_btc","side":"buy","price":"0.0121","size":"53"},
    {"symbol":"bch_btc","side":"buy","price":"0.0121","size":"54"},
    {"symbol":"bch_btc","side":"buy","price":"0.0121","size":"55"}
]"
}
```
#### 返回参数

参数名	|类型	|描述
------------- | ------------- |------------- 
result|	Boolean	|下单结果。若是下单失败，将给出错误码提示
order_id	|String	|订单ID
#### 返回示例
```
{
    "bch_btc":[
        {
            "order_id":"144670631",
            "result":true
        },
        {
            "order_id":"144670632",
            "result":true
        },
        {
            "order_id":"144670633",
            "result":true
        },
        {
            "order_id":"144670634",
            "result":true
        },
        {
            "order_id":"144670635",
            "result":true
        }
    ]
}
   
```
####解释说明
```
symbol : symbol必须是有效的币对。币对列表可通过/ symbols接口获取。

type : 下单时，暂时只能下限价单，限价单需要指定price和size。size是买入或卖出交易货币的数量，price表示每个交易货币相对计价
货币的价格。限价单会按指定价格或更好的价格成交。根据市场条件，卖单可以按照指定价格或者更高价格来
成交，买单可以按照指定价格或更低价格成交。如果限价单不能立即成交，那么限价单将进入深度列表，直到
被另一个新订单成交或被用户撤单。 

price : price表示买入或卖出的价格。

size : size表示买入或卖出交易货币的数量
```
> ####**注意:批量下单最多只能下5个！！**

### 7.单个订单撤单
#### HTTP请求
```
POST /api/v2/cancelOrders
```
#### 请求示例
```
/api/v2/cancelOrders
```
#### 请求参数
参数名	|类型	|是否必填|描述
------------- | ------------- |------------- |------------- 
key	|String	|是|	API key
signature	|String|	是|	签名
symbol|	String	|是	|交易对名称 列：bch_btc
order_id|	String	|是	|订单ID
#### 返回参数

参数名	|类型	|描述
------------- | ------------- |------------- 
result|	Boolean	|撤单结果。若是撤单失败，将给出错误码提示
order_id	|String	|订单ID
#### 返回示例
```
{
    "result":true,
    "order_id":"144670619"
}
   
```

### 8.多个订单撤单
#### HTTP请求
```
POST /api/v2/cancelBatchOrders
```
#### 请求示例
```
/api/v2/cancelBatchOrders
[
    {
        "symbol":"bch_btc",
        "order_id":"144670624"
    },
    {
        "symbol":"bch_btc",
        "order_id":"144670623"
    },
    {
        "symbol":"bch_btc",
        "order_id":"144670622"
    }
]
```
#### 请求参数

参数名	|类型	|是否必填|描述
------------- | ------------- |-------------  |------------- 
key	| String| 	是|  	API key
signature	| String	| 是| 	签名
order_ids| 	list | String| 	是	| 订单列表
symbol	| String| 	是	| 交易对名称 列：bch_btc
order_id	| String| 	是	| 订单ID
#### 返回参数

参数名	|类型	|描述
------------- | ------------- |------------- 
result	|Boolean	|撤单结果。若是撤单失败，将给出错误码提示
order_id	|String	|订单ID
#### 返回示例
```
{
    "bch_btc":[
        {
            "order_id":"144670623",
            "result":true
        },
        {
            "order_id":"144670622",
            "result":true
        },
        {
            "order_id":"144670621",
            "result":true
        },
        {
            "order_id":"144670620",
            "result":false
        }
    ],
    "eth_btc":[
        {
            "order_id":"144670620",
            "result":true
        },
        {
            "order_id":"144670620",
            "result":true
        }
    ]
}
   
```
> ####**注意:批量撤单最多只能撤10个！！**

### 9.获取所有订单列表
#### HTTP请求
```
POST /api/v2/ordersList/
```
#### 请求参数

参数名	|类型	|是否必填|描述
------------- | ------------- |-------------  |------------- 
key	|String|	是|	API key
signature|	String	|是|	签名
symbol	|String	|是|	交易对名称 列：bch_btc
state	|String	|否	|订单状态 1:挂单成功 2:开放交易 3:取消 4:完成
from	|String	|否	|订单开始的时间戳
to	|String	|否	|订单结束的时间戳
#### 返回参数

参数名	|类型	|描述
------------- | ------------- |------------- 
symbol	|String|	币对名称
order_id	|String	|订单ID
timestamp	|String|	订单创建时间
side	|String	|buy or sell
type	|String	|limit,market(默认是limit)
status	|String	|订单状态 1:挂单成功 2:开放交易 3:取消 4:完成
price	|String	|委托价格
unit_price|	String|	成交均价
size	|String	|委托数量（交易货币数量）
limit_size|	String	|未成交数量
filled_size	|String	|已成交数量
filled_notional|	String	|已成交金额
#### 返回示例
```
[
    {
        "symbol":"bch_btc",
        "order_id":"144670625",
        "timestamp":"2019-05-20 16:34:13",
        "side":"buy",
        "type":"market",
        "status":"1",
        "price":"0",
        "unit_price":"0",
        "size":"0",
        "limit_size":"0",
        "filled_size":"0",
        "filled_notional":"0"
    },
    {
        "symbol":"bch_btc",
        "order_id":"144670624",
        "timestamp":"2019-05-20 16:23:47",
        "side":"buy",
        "type":"limit",
        "status":"2",
        "price":"0.0121",
        "unit_price":"0",
        "size":"50",
        "limit_size":"50",
        "filled_size":"0",
        "filled_notional":"0"
    },
    {
        "symbol":"bch_btc",
        "order_id":"144670623",
        "timestamp":"2019-05-20 16:23:22",
        "side":"buy",
        "type":"limit",
        "status":"2",
        "price":"0.0121",
        "unit_price":"0",
        "size":"50",
        "limit_size":"50",
        "filled_size":"0",
        "filled_notional":"0"
    },
]
   
```
#### 返回注意
```
默认最大20条 按照ID的大小倒序排列。
```	
### 10.获取未成交的订单列表 
#### HTTP请求
```
POST /api/v2/ordersList/
```
#### 请求参数

参数名	|类型	|是否必填|描述
------------- | ------------- |-------------  |------------- 
key	|String|	是|	API key
signature|	String	|是|	签名
symbol	|String	|是|	交易对名称 列：bch_btc
state	|String|	否	|订单状态 2:开放交易
from	|String	|否	|订单开始的时间戳
to	|String	|否	|订单结束的时间戳
#### 返回参数

参数名	|类型	|描述
------------- | ------------- |------------- 
symbol	|String|	币对名称
order_id	|String	|订单ID
timestamp	|String|	订单创建时间
side	|String	|buy or sell
type	|String	|limit,market(默认是limit)
status	|String	|订单状态 1:挂单成功 2:开放交易 3:取消 4:完成
price	|String	|委托价格
unit_price|	String|	成交均价
size	|String	|委托数量（交易货币数量）
limit_size|	String	|未成交数量
filled_size	|String	|已成交数量
filled_notional|	String	|已成交金额
#### 返回示例
```
[
    {
        "symbol":"bch_btc",
        "order_id":"144670624",
        "timestamp":"2019-05-20 16:23:47",
        "side":"buy",
        "type":"limit",
        "status":"2",
        "price":"0.0121",
        "unit_price":"0",
        "size":"50",
        "limit_size":"50",
        "filled_size":"0",
        "filled_notional":"0"
    },
    {
        "symbol":"bch_btc",
        "order_id":"144670623",
        "timestamp":"2019-05-20 16:23:22",
        "side":"buy",
        "type":"limit",
        "status":"2",
        "price":"0.0121",
        "unit_price":"0",
        "size":"50",
        "limit_size":"50",
        "filled_size":"0",
        "filled_notional":"0"
    },
    {
        "symbol":"bch_btc",
        "order_id":"144670622",
        "timestamp":"2019-05-20 16:20:37",
        "side":"buy",
        "type":"limit",
        "status":"2",
        "price":"0.0111",
        "unit_price":"0",
        "size":"50",
        "limit_size":"50",
        "filled_size":"0",
        "filled_notional":"0"
    },
    {
        "symbol":"bch_btc",
        "order_id":"144670621",
        "timestamp":"2019-05-20 16:18:15",
        "side":"buy",
        "type":"limit",
        "status":"2",
        "price":"0.0111",
        "unit_price":"0",
        "size":"50",
        "limit_size":"50",
        "filled_size":"0",
        "filled_notional":"0"
    },
    {
        "symbol":"bch_btc",
        "order_id":"144670619",
        "timestamp":"2019-05-20 15:56:09",
        "side":"buy",
        "type":"limit",
        "status":"2",
        "price":"0.0111",
        "unit_price":"0",
        "size":"50",
        "limit_size":"50",
        "filled_size":"0",
        "filled_notional":"0"
    }
]
   
```	
####返回注意
```
默认最大20条 按照ID的大小倒序排列。
```	
### 11.获取某一个订单的详情 
通过订单ID获取单个订单信息
#### HTTP请求
```
POST /api/v2/ordersView/
```
#### 请求参数

参数名	|类型	|是否必填|描述
------------- | ------------- |-------------  |------------- 
key|	String	|是	|API key
signature|String	|是|	签名
symbol|	String	|是	|交易对名称 列：bch_btc
order_id	|String|	是	|订单ID
#### 返回参数

参数名	|类型	|描述
------------- | ------------- |------------- 
symbol	|String|	币对名称
order_id	|String|	订单ID
timestamp|	String	|订单创建时间
side	|String|	buy or sell
type	|String|	limit,market(默认是limit)
status	|String	|订单状态 1:挂单成功 2:开放交易 3:取消 4:完成
price	|String	|委托价格
unit_price|	String	|成交均价
size	|String	|委托数量（交易货币数量）
limit_size|	String|	未成交数量
filled_size	|String	|已成交数量
filled_notional|	String|	已成交金额
#### 返回示例
```
{
    "symbol":"bch_btc",
    "order_id":"144670620",
    "timestamp":"2019-05-20 16:07:38",
    "side":"buy",
    "type":"limit",
    "status":"3",
    "price":"0.0111",
    "unit_price":"0",
    "size":"50",
    "limit_size":"50",
    "filled_size":"0",
    "filled_notional":"0"
}
   
```
## 钱包API接口 
### 1.单一币种转入转出记录
#### HTTP请求
```
POST /api/v2/transferList
```
#### 请求参数

参数名	|类型	|是否必填|描述
------------- | ------------- |-------------  |------------- 
key	|String	|是|	API key
signature	|String	|是|	签名
type	|String|	是|	'in', 'out'
coin	|String|	是|	币种名称 btc eth
from	|String|	否	|起始时间戳
size	|String|	否	|查询长度 默认 20 最长50
#### 返回参数

参数名	|类型	|描述
------------- | ------------- |------------- 
id	|String	|转账ID
type	|String|	'in', 'out'
tx-hash	|String	|交易哈希
address	|String	|地址
amount	|String	|个数
fee	|String|	手续费
status|	String|	状态 0、未确认、1待确认、2确认中、3撤销、4失败、5已补转、6成功
created-at|	String	|发起时间
#### 返回示例
```
[
    {
        "id":"74760",
        "type":"in",
        "tx-hash":"ddc686225979692b6fffce5033aa339cd75fc4c391d87f65bebe13922ba3805b",
        "address":"1GR5pxJ7neeyg7BfwtNXjoCKaZ596hxR4L",
        "amount":"0.10000000",
        "fee":"0.00000000",
        "status":"6",
        "created-at":"1556218140"
    },
    {
        "id":"74757",
        "type":"in",
        "tx-hash":"2b33f944d08386392871a803bd4057bd1e15ad0b558220befe29edf78033cdfb",
        "address":"1PEXRcJ3tWLFW8uBoricd2DDUxtUr2Hbcr",
        "amount":"0.08667380",
        "fee":"0.00000000",
        "status":"6",
        "created-at":"1556204860"
    },
    {
        "id":"74756",
        "type":"in",
        "tx-hash":"a8ef9f6d855813c1ecb34d12e00ab3d8e0f0e7dad54ad4926436f8566d9a2a8f",
        "address":"1LNzHU1bQigw6BZHG8vMvcr67RrbYQf69s",
        "amount":"0.01100000",
        "fee":"0.00000000",
        "status":"6",
        "created-at":"1556199476"
    },
    {
        "id":"7613",
        "type":"in",
        "tx-hash":"180f7fb86fff90f84ccc03154b25afd3bccd1ebe130289fbc6065696fe9925f5",
        "address":"16ar4B2VzTVqd2kQFHrF2z2dPU7kcfJUfw",
        "amount":"0.09080000",
        "fee":"0.00000000",
        "status":"6",
        "created-at":"1512805165"
    }
]
   
```
## 错误信息
#### 错误信息会以以下格式返回

```
{"error_code":"100","error_message":"必选参数不能为空"} 

```
## 错误代码表
错误代码		|详细描述
------------- | ------------- 
100	|必选参数不能为空
101	|非法参数
102	|请求的虚拟币不存在
103	|密钥不存在
104	|签名不匹配
105	|权限不足
106	|请求过期(nonce错误)
200	|余额不足
201	|买卖的数量小于最小买卖额度
202	|下单价格必须在 0 - 1000000 之间
203	|订单不存在
204	|挂单金额必须在 0.001 BTC以上
205|限制挂单价格
301|记录不存在
302|记录不存在
303|您的挂单正在被处理，请稍候再试
304	|撤消失败，您的挂单已被处理
206	|小数位错误
401	|系统错误
402|求过于频繁
403|非开放API
404|IP限制不能请求该资源
405|币种交易暂时关闭
406|系统错误
407|系统错误
408|系统错误
409|系统错误
410|用户资产有误
411|用户资产不足
412|暂不可借贷
413|借贷数量不足
414|借贷系统错误
415|借贷资产有误
416|借贷手续费错误
417|交易对暂不可挂单
418|利息还款失败
419|欠款还款失败
420|还款记录添加失败
421|成交额太小
422|成交额太大
423|未开放交易
424|建议挂单价格有误
425|价格小数位数有误
426|价格小数位数有误
427|挂单数量太小
428|委托不存在
429|撤消失败，您的挂单已被处理
430|用户未设置交易密码
431|划转用户类型错误
432|交易对错误
433|需要登录
434|用户登录状态已失效
435|用户交易密码错误
436|借贷账户状态异常
437|币种不存在
438|站点不存在
439|不支持转入所选币种
440|该币种不支持当前方式划转
441|交易密码错误次数过多
