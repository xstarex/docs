REST API 简介
-------------

xstarex为用户提供了一套全新的API，可以帮用户快速接入xstarex的交易系统，实现程序化交易。

通过API可以实现以下功能：

-   配置信息查询

-   市场行情查询

-   交易信息查询

-   资产信息查询
----------------------
### 签名认证


### 安全认证

目前关于apikey申请和修改，请在“个人中心 -
API管理”页面进行相关操作。其中AccessKey为API
访问密钥，SecretKey为用户对请求进行签名的密钥（仅申请并平台审核通过后可见）。

**重要提示：这两个密钥与账号安全紧密相关，无论何时都请勿向其它人透露。**

### 合法请求结构

基于安全考虑，所有的 API
请求都必须进行签名运算。一个合法的请求由以下几部分组成：

API 访问密钥（AccessKey） 您申请的API Key。

签名方法（SignatureMethod） 用户计算签名的基于哈希的协议，此处使用 HmacSHA256。

时间戳（Timestamp）
您发出请求的时间 。在请求中包含此值有助于防止第三方截取您的请求。

随机字符串（nonce）
请求的唯一标识。在请求中包含此值有助于防止第三方截取您的请求，防止重放攻击。

签名 签名计算得出的值，用于确保签名有效和未被篡改

例：

```markdown
https://www.xstarex.com/coinex-interface/coinex-interface/api/v1/account/assets?timestamp=1530864522611&nonce=5bl727&accessKey=eoxxxxxxxxxxxxxxxxxxSoi3xxxxxxxxxANSbsXcP&signature=calculated value
```

### 签名运算

API 请求在通过 Internet
发送的过程中极有可能被篡改。为了确保请求未被更改，我们会要求用户在每个请求中带上签名，来校验参数或参数值在传输途中是否发生了更改。

**计算签名所需的步骤**：

1.  按照请求参数名的字母升序排列非空请求参数（这里包含AccessKey，不包含signature和secretKey），使用URL键值对的格式（即key1=value1&key2=value2…）拼接成字符串stringA；

2.  在stringA最后拼接上Secretkey得到字符串stringSignTemp；

3.  对stringSignTemp进行HmacSHA256运算，得到signature值。

请求说明
--------

1.  访问地址 [https://www.xstarex.com]

2.  在请求路径前都加上项目路径*/coinex-interface*

3.  所有请求参数请按照 API 说明进行参数封装。

4.  将封装好参数的 API 请求通过 POST 或 GET 的方式提交到服务器。

5.  xstarex处理请求，并返回相应的 JSON 格式结果。

6.  请使用https请求

7.  请求头中header设置成en_US，返回英文msg;默认或者设置成zh_CN，返回中文简体msg。

8.  请求中交易对名称和币种名称不区分大小写。

9.  请求时间格式统一为yyyy-MM-dd。

API Reference
-------------

### 接口列表

| 接口数据类型 | 请求方法                                                                   | 类型 | 描述                                                         | 接口安全        |
|--------------|----------------------------------------------------------------------------|------|--------------------------------------------------------------|-----------------|
| 配置信息     | [/api/v1/basic/coinpairs](#get-apiv1basiccoinpairs-获取已开启的交易对信息) | GET  | 获取已开启的交易对信 息，包括价格小数点位数、 数量小数点位数 | 需要ApiKey      |
| 配置信息     | [/api/v1/basic/systemclock](#get-apiv1basicsystemclock-获取系统当前时间)   | GET  | 获取当前系统时间                                             | 需要ApiKey      |
| 市场行情     | [/api/v1/market/tickers](#get-apiv1markettickers-获取行情)                 | GET  | 查询行情                                                     | 需要ApiKey      |
| 市场行情     | [/api/v1/market/kline](#get-apiv1marketkline-获取k线)                      | GET  | 查询K线                                                      | 需要ApiKey      |
| 市场行情     | [/api/v1/market/depth](#get-apiv1marketdepth-获取深度)                     | GET  | 查询深度                                                     | 需要ApiKey      |
| 市场行情     | [/api/v1/market/trade](#get-apiv1markettrades-获取成交记录)*s*             | GET  | 获取成交记录                                                 | 需要ApiKey      |
| 交易信息     | [/api/v1/order/new](#post-apiv1ordernew-委托下单)                          | POST | 委托下单                                                     | 需要ApiKey+签名 |
| 交易信息     | [/api/v1/order/cancel](#post-apiv1ordercancel-取消委托)                    | POST | 取消委托                                                     | 需要ApiKey+签名 |
| 交易信息     | [/api/v1/order/{orderId}](#get-apiv1orderid-获取单个委托单详情)            | GET  | 获取某个委托                                                 | 需要ApiKey+签名 |
| 交易信息     | [/api/v1/order/latest](#get-apiv1orderlatest-获取当前委托记录)             | GET  | 查询当前委托                                                 | 需要ApiKey+签名 |
| 交易信息     | [/api/v1/order/history](#get-apiv1orderhistory-获取历史委托记录)           | GET  | 查询历史委托                                                 | 需要ApiKey+签名 |
| 交易信息     | [/api/v1/order/filled](#get-apiv1orderfilled-获取用户成交记录)             | GET  | 查询用户成交记录                                             | 需要ApiKey+签名 |
| 资产信息     | [/api/v1/account/assets](#get-apiv1accountassets-获取用户资产)             | GET  | 获取账户资产列表                                             | 需要ApiKey+签名 |

在调用接口时，请添加get参数。key为accessKey，value为网页上申请的api
key。Key为timestamp，value为当前系统时间戳。Key为nonce,value为随机字符串。Key为signature，value为计算得到的签名。计算签名方式请参考上述[签名认证](#签名认证)。

（注：secretKey不参与参数传输，仅作为计算签名使用，请妥善保管。）

例：

```markdown
https://www.xstarex.com/coinex-interface/api/v1/account/assets?accessKey=eoCmCQCSVg2dtezoZYHBKBh0IySoi3eQ9igzwa2YhuIU2cnI22nksk4LANSbsXcP&nonce=ok1iie&signature=0264197b2810443b5a29308eddbf3d79&timestamp=1532164460267
```

### 配置信息

#### GET /api/v1/basic/coinpairs 获取已开启的交易对信息

请求参数:

| 字段      | 备注    | 是否必须 |
|-----------|---------|----------|
| accessKey | api_key | 是       |

响应数据:

| 参数名称 | 描述         | 是否必须 |
|----------|--------------|----------|
| status   | 状态码       | 是       |
| msg      | 响应信息描述 | 是       |
| data     | 响应数据     | 否       |

data说明:

```markdown
"data": [ { "name": 名称, "pricePrecision": 价格精度, "quantityPrecision": 数量精度 } ] 
```



请求响应示例：

```markdown

/* 
/api/v1/basic/coinpairs?accessKey=eoCmCQCSVg2dtezoZYHBKBh0IySoi3eQ9igzwa2YhuIU2cnI22nksk4LANSbsXcP
*/
{
	"status": 1,
	"msg": "查询所有交易对成功",
	"data": [{
		"name": "ETH/BTC",
		"pricePrecision": 6,
		"quantityPrecision": 4
	}
//more data here
]
}
/*	/api/v1/basic/coinpairs		*/
{
	"status": 801,
	"msg": "accessKey is not provided"
}


```


#### GET /api/v1/basic/systemclock 获取系统当前时间

请求参数:

| 字段      | 备注    | 是否必须 |
|-----------|---------|----------|
| accessKey | api_key | 是       |

响应数据:

| 参数名称 | 描述         | 是否必须 |
|----------|--------------|----------|
| status   | 状态码       | 是       |
| msg      | 响应信息描述 | 是       |
| data     | 响应数据     | 否       |

data说明：

```markdown
"data": 系统当前时间戳
```

请求响应例子：

```markdown
/* /api/v1/basic/systemclock?accessKey=eoCmCQCSVg2dtezoZYHBKBh0IySoi3eQ9igzwa2YhuIU2cnI22nksk4LANSbsXcP	
*/
{
	"status": 1,
	"msg": "获取系统时间成功",
	"data": 1531535781791
}
/*	/api/v1/basic/systemclock 	*/
{
	"status": 801,
	"msg": "accessKey is not provided"
}

```

### 市场行情

#### GET /api/v1/market/tickers 获取行情

请求参数:

| 字段      | 备注                                                                                     | 是否必须 |
|-----------|------------------------------------------------------------------------------------------|----------|
| accessKey | api_key                                                                                  | 是       |
| coinPair  | 交易对名称                                                                               | 否       |
|           | 如果传值（例: ETH/BTC）,查询这个交易对的行情；如果不传值，查询所有交易对的行情           |          |

响应数据:

| 参数名称 | 描述         | 是否必须 |
|----------|--------------|----------|
| status   | 状态码       | 是       |
| msg      | 响应信息描述 | 是       |
| data     | 响应数据     | 否       |


data说明：


```markdown
"data": [{
		"coinPair": 交易对名称,
		"newPrice": 最新价格,
		"priceIncrease": 24小时涨幅,
		"topPrice": 24小时最高价格,
		"bottomPrice": 24小时最低价格,
		"gross": 24小时交易总额,
		"grossVolume": 24小时交易总量
	}]

```


请求响应例子：


```markdown
/*	
/api/v1/market/tickers?accessKey=eoCmCQCSVg2dtezoZYHBKBh0IySoi3eQ9igzwa2YhuIU2cnI22nksk4LANSbsXcP
*/
{
	"status": 1,
	"msg": "操作成功",
	"data": [{
		"coinPair": "ETH/BTC",
		"newPrice": 0,
		"priceIncrease": 0,
		"topPrice": 0,
		"bottomPrice": 0,
		"gross": 0,
		"grossVolume": 0
	}
//more data here
]
}
/*	/api/v1/market/tickers?accessKey=eoCmCQCSVg2dtezoZYHBKBh0IySoi3eQ9igzwa2YhuIU2cnI22nksk4LANSbsXcP&coinPair=LTC/BTC
*/
{
	"status": 1,
	"msg": "操作成功",
	"data": [{
		"coinPair": "LTC/BTC",
		"newPrice": 0,
		"priceIncrease": 0,
		"topPrice": 0,
		"bottomPrice": 0,
		"gross": 0,
		"grossVolume": 0
	}]
}
/*	/api/v1/market/tickers?coinPair=LTC/BTC	*/
{
	"status": 801,
	"msg": "accessKey is not provided"
}

```



#### GET /api/v1/market/kline 获取K线

请求参数:

| 字段      | 备注                        | 是否必须 |
|-----------|-----------------------------|----------|
| accessKey | api_key                     | 是       |
| limit     | 要几条，1-1000，不传默认150 | 否       |
| coinPair  | 交易对名称(例:LTC/BTC)      | 是       |
| period    | K线类型，取值[1min,5min]    | 是       |

响应数据:

| 参数名称 | 描述         | 是否必须 |
|----------|--------------|----------|
| status   | 状态码       | 是       |
| msg      | 响应信息描述 | 是       |
| data     | 响应数据     | 否       |

data说明：

```markdown
"data": [{
		"time": 时间,
		"open": 开盘价格,
		"high": 最高价格,
		"low": 最低价格,
		"close": 收盘价格,
		"quantity": 成交量
	}
]

```

请求响应例子：

```markdown
/*	
/api/v1/market/kline?accessKey=eoCmCQCSVg2dtezoZYHBKBh0IySoi3eQ9igzwa2YhuIU2cnI22nksk4LANSbsXcP&coinPair=EOS/BTC&period=1min
*/
{
	"status": 1,
	"msg": "操作成功",
	"data": [{
		"time": 1530005880000,
		"open": 0.1,
		"high": 0.1,
		"low": 0.1,
		"close": 0.1,
		"quantity": 0
	},
//more data here
]
}
/*
/api/v1/market/kline?accessKey=eoCmCQCSVg2dtezoZYHBKBh0IySoi3eQ9igzwa2YhuIU2cnI22nksk4LANSbsXcP&coinPair=EOS/BTC&period=min
*/
{
	"status": 60027,
	"msg": "不合法k线类型"
}
/* /api/v1/market/kline?accessKey=eoCmCQCSVg2dtezoZYHBKBh0IySoi3eQ9igzwa2YhuIU2cnI22nksk4LANSbsXcP&limit=1001&coinPair=EOS/BTC&period=1min 
*/
{
	"status": 60026,
	"msg": "不合法记录数，正常范围:[1,1000]"
}
/* /api/v1/market/kline?accessKey=eoCmCQCSVg2dtezoZYHBKBh0IySoi3eQ9igzwa2YhuIU2cnI22nksk4LANSbsXcP&limit=1&period=1min
*/
{
	"status": 60028,
	"msg": "交易对名称不能为空"
}

```

#### GET /api/v1/market/depth 获取深度

请求参数:

| 字段      | 备注                      | 是否必须 |
|-----------|---------------------------|----------|
| accessKey | api_key                   | 是       |
| coinPair  | 交易对名称（例: EOS/ETH） | 是       |

响应数据:

| 参数名称 | 描述         | 是否必须 |
|----------|--------------|----------|
| status   | 状态码       | 是       |
| msg      | 响应信息描述 | 是       |
| data     | 响应数据     | 否       |

data说明：

```markdown
"data": {
		"coinPair": 交易对名称,
		"asks": [{                  卖方深度
			"price": 挂单价格,
			"quantity": 剩余数量
		}],
		"bids": [{                  买方深度
			"price": 挂单价格,
			"quantity": 剩余数量
		}]
	}

```

请求响应例子：

```markdown
/*	
/api/v1/market/depth?accessKey=eoCmCQCSVg2dtezoZYHBKBh0IySoi3eQ9igzwa2YhuIU2cnI22nksk4LANSbsXcP&coinPair=EOS/ETH
*/
{
	"status": 1,
	"msg": "操作成功",
	"data": {
		"coinPair": "EOS/ETH",
		"asks": [{
			"price": "1.000000",
			"quantity": "1.0000"
		}
//more data here
],
		"bids": [{
			"price": "1.000000",
			"quantity": "1.0000"
		},
//more data here
]
	}
}
/*	
/api/v1/market/depth?accessKey=eoCmCQCSVg2dtezoZYHBKBh0IySoi3eQ9igzwa2YhuIU2cnI22nksk4LANSbsXcP
*/
{
	"status": 60028,
	"msg": "交易对名称不能为空"
}

```

#### GET /api/v1/market/trades 获取成交记录

请求参数:

| 字段      | 备注                       | 是否必须 |
|-----------|----------------------------|----------|
| accessKey | api_key                    | 是       |
| limit     | 要几条，1-200，不传默认200 | 否       |
| startTime | 开始时间(格式：yyyy-MM-dd) | 否       |
| endTime   | 结束时间(格式：yyyy-MM-dd) | 否       |
| coinPair  | 交易对名称(例:EOS:ETH)     | 是       |
| direction | 交易方向，0买，1卖         | 否       |

响应数据:

| 参数名称 | 描述         | 是否必须 |
|----------|--------------|----------|
| status   | 状态码       | 是       |
| msg      | 响应信息描述 | 是       |
| data     | 响应数据     | 否       |

data说明：

```markdown
"data": [
{
         "id ": 成交记录id,
		"coinPair ": 交易对名称,
		"price": 成交价格,
		"quantity": 成交数量,
		"time": 成交时间,
		"direction": 成交方向 0买 1卖
	}
]

```

请求响应例子：

```markdown
/*	/api/v1/market/trade?accessKey=eoCmCQCSVg2dtezoZYHBKBh0IySoi3eQ9igzwa2YhuIU2cnI22nksk4LANSbsXcP&endTime=2018-07-06&coinPair=EOS/ETH&startTime=2018-06-27	
*/
{
	"status": 1,
	"msg": "操作成功",
	"data": [{
         "id": "815300319787684",
		"coinPair": "EOS/ETH",
		"price": "10.000000",
		"quantity": "1.0000",
		" time": 1530032107000,
		"direction": 0
	}
//more data here
]
}
/*	
/api/v1/market/trade?endTime=2018-07-06&coinPair=EOS/ETH&startTime=2018-06-27
*/
{
	"status": 801,
	"msg": "accessKey is not provided"
}

```


### 交易信息

#### POST /api/v1/order/new 委托下单

请求参数:

| 字段      | 备注                    | 是否必须 |
|-----------|-------------------------|----------|
| timestamp | 当前时间戳              | 是       |
| accessKey | api_key                 | 是       |
| nonce     | 随机字符串              | 是       |
| signature | 签名                    | 是       |
| coinPair  | 交易对名称(例: EOS/ETH) | 是       |
| price     | 价格                    | 是       |
| quantity  | 数量                    | 是       |
| direction | 交易方向，0买，1卖      | 是       |

响应数据:

| 参数名称 | 描述         | 是否必须 |
|----------|--------------|----------|
| status   | 状态码       | 是       |
| msg      | 响应信息描述 | 是       |
| data     | 响应数据     | 否       |

data说明：

```markdown
"data": {
		"dealId": 委托单id
}

```


请求响应例子：

```markdown
/*  /api/v1/order/new	*/
{
	"status": 1,
	"msg": "操作成功",
	"data": {
		"dealId": 115317156395661
	}
}
/*	/api/v1/order/new 	*/
{
	"status": 809,
	"msg": "signature is not valid"
}

```


#### POST /api/v1/order/cancel 取消委托

请求参数:

| 字段      | 备注       | 是否必须 |
|-----------|------------|----------|
| timestamp | 当前时间戳 | 是       |
| accessKey | api_key    | 是       |
| nonce     | 随机字符串 | 是       |
| signature | 签名       | 是       |
| id        | 委托单id   | 是       |

响应数据:

| 参数名称 | 描述         | 是否必须 |
|----------|--------------|----------|
| status   | 状态码       | 是       |
| msg      | 响应信息描述 | 是       |

请求响应例子：

```markdown
/*	/api/v1/order/cancel	*/
{
	"status": 1,
	"msg": "操作成功"
}
/*	/api/v1/order/cancel	*/
{
	"status": 809,
	"msg": "signature is not valid"
}

```


#### GET /api/v1/order/{id} 获取单个委托单详情

请求参数:

| 字段      | 备注       | 是否必须 |
|-----------|------------|----------|
| timestamp | 当前时间戳 | 是       |
| accessKey | api_key    | 是       |
| nonce     | 随机字符串 | 是       |
| signature | 签名       | 是       |
| id        | 委托单id   | 是       |

响应数据:

| 参数名称 | 描述         | 是否必须 |
|----------|--------------|----------|
| status   | 状态码       | 是       |
| msg      | 响应信息描述 | 是       |
| data     | 响应数据     | 否       |

data说明：

```markdown
"data": {
         "id": 委托单id,
		"coinPair": 交易对,
		"coinSymbol": 交易币种,
		"currencySymbol": 定价币种,
		"direction": 交易方向 0买 1卖,
		"price": 委托价格,
		"quantity": 委托数量,
		"money": 委托金额,
		"successQuantity": 已成交数量,
		"dealPercent": 成交百分比,
		"leftQuantity": 未成交数量,
		"status": 状态 1未成交 2部分成交 3完全成交 4撤销
	}

```

请求响应例子：

```markdown
/*	/api/v1/order/115317145432508?accessKey=eoCmCQCSVg2dtezoZYHBKBh0IySoi3eQ9igzwa2YhuIU2cnI22nksk4LANSbsXcP&nonce=j4p6ol&signature=edc393cf5d61ca4363f06e011f250d3a&timestamp=1532155990072	
*/
{
	"status": 1,
	"msg": "操作成功",
	"data": {
         "id": "815323420064329",
		"coinPair": "ETH/BTC",
		"coinSymbol": "ETH",
		"currencySymbol": "BTC",
		"direction": 0,
		"price": "1.000000",
		"quantity": "1.0000",
		"money": "1.000000",
		"successQuantity": "1.0000",
		"dealPercent": "100.00%",
		"leftQuantity": "0.0000",
		"status": 1
	}
}
/*	/api/v1/order/115317145432508?accessKey=eoCmCQCSVg2dtezoZYHBKBh0IySoi3eQ9igzwa2YhuIU2cnI22nksk4LANSbsXcP&nonce=88lhy3&signature=219c598a5876ff1cf87dffc7d2dfe466&timestamp=1532156109248	*/
{
	"status": 809,
	"msg": "signature is not valid"
}

```


#### GET /api/v1/order/latest 获取当前委托记录

请求参数:

| 字段      | 备注                        | 是否必须 |
|-----------|-----------------------------|----------|
| timestamp | 当前时间戳                  | 是       |
| accessKey | api_key                     | 是       |
| nonce     | 随机字符串                  | 是       |
| signature | 签名                        | 是       |
| limit     | 要几条，[1,100],不传默认100 | 是       |
| startTime | 开始时间，格式：yyyy-MM-dd  | 否       |
| endTime   | 结束时间，格式：yyyy-MM-dd  | 否       |
| coinPair  | 交易对名称(例：ETH/BTC)     | 是       |
| direction | 交易方向，0买，1卖          | 否       |

响应数据:

| 参数名称 | 描述         | 是否必须 |
|----------|--------------|----------|
| status   | 状态码       | 是       |
| msg      | 响应信息描述 | 是       |
| data     | 响应数据     | 否       |

data说明：

```markdown
"data": [
{
"id": 委托单id,
         "coinPair": 交易对,
		"coinSymbol": 交易币种,
		"currencySymbol": 定价币种,
		"direction": 交易方向 0买 1卖,
		"price": 委托价格,
		"quantity": 委托数量,
		"money": 委托金额,
		"successQuantity": 已成交数量,
		"dealPercent": 成交百分比,
		"leftQuantity": 未成交数量,
"status": 委托状态 1未成交 2部分成交 3完全成交 4撤销
	}
]

```


请求响应例子：

```markdown
/*	/api/v1/order/latest?accessKey=eoCmCQCSVg2dtezoZYHBKBh0IySoi3eQ9igzwa2YhuIU2cnI22nksk4LANSbsXcP&nonce=cg7l27&coinPair=ETH/BTC&signature=d32024e34fa6cee0b7d8d3d08af57d17&timestamp=1532159156722
*/
{
	"status": 1,
	"msg": "操作成功",
	"data": [{
         "id": "115317143318292",
         "coinPair": "ETH/BTC",
		"coinSymbol": "ETH",
		"currencySymbol": "BTC",
		"direction": 1,
		"price": "1.000000",
		"quantity": "1.0000",
		"money": "1.000000",
		"successQuantity": "0.0000",
		"dealPercent": "0.00%",
		"leftQuantity": "1.0000",
		"status": 1
	},
//more data here
]
}
/*	/api/v1/order/latest?nonce=31g1so&coinPair=ETH/BTC&signature=09f1de964adf4d47f42e9ad6a34af8bf&timestamp=1532159400708
*/
{
	"status": 801,
	"msg": "accessKey is not provided"
}

```


#### GET /api/v1/order/history 获取历史委托记录

请求参数:

| 字段      | 备注                        | 是否必须 |
|-----------|-----------------------------|----------|
| timestamp | 当前时间戳                  | 是       |
| accessKey | api_key                     | 是       |
| nonce     | 随机字符串                  | 是       |
| signature | 签名                        | 是       |
| limit     | 要几条，[1,100],不传默认100 | 是       |
| startTime | 开始时间                    | 否       |
| endTime   | 结束时间                    | 否       |
| coinPair  | 交易对名称(例：ETH/BTC)     | 是       |
| direction | 交易方向，0买，1卖          | 否       |

响应数据:

| 参数名称 | 描述         | 是否必须 |
|----------|--------------|----------|
| status   | 状态码       | 是       |
| msg      | 响应信息描述 | 是       |
| data     | 响应数据     | 否       |

data说明：

```markdown
"data": [{
         “id”:委托记录id,
         "coinPair": 交易对,
		"coinSymbol": 交易币种,
		"currencySymbol": 定价币种,
		"direction": 交易方向 0买 1卖,
		"price": 委托价格,
		"quantity": 委托数量,
		"money": 委托金额,
		"successQuantity": 已成交数量,
		"dealPercent": 成交百分比,
		"dealPrice": 成交均价,
		"dealMoney": 已成交金额,
"status":委托状态 1未成交 2部分成交 3完全成交 4撤销
	}]

```


请求响应例子：

```markdown
/*	/api/v1/order/history?accessKey=eoCmCQCSVg2dtezoZYHBKBh0IySoi3eQ9igzwa2YhuIU2cnI22nksk4LANSbsXcP&endTime=2018-07-06&nonce=8aw5cw&coinPair=EOS/ETH&signature=b706de536d26a07ff4ea61d36db1081c&startTime=2018-06-27&timestamp=1532161334713&direction=0	*/
{
	"status": 1,
	"msg": "操作成功",
	"data": [{
		"id": "815300311770015",
		"coinPair": "EOS/ETH",
		"coinSymbol": "EOS",
		"currencySymbol": "ETH",
		"direction": 0,
		"price": "10.000000",
		"quantity": "1.0000",
		"money": "10.000000",
		"successQuantity": "1.0000",
		"dealPercent": "100.00%",
		"dealPrice": "10.000000",
		"dealMoney": "20.000000",
		"status": 3
	}
//more data here
]
}
/*	
/api/v1/order/history?endTime=2018-07-06&nonce=8kx597&coinPair=EOS/ETH&signature=d28dd124aa5560defcb50dbdbd1e3fd3&startTime=2018-06-27&timestamp=1532161708916&direction=0
*/
{
	"status": 801,
	"msg": "accessKey is not provided"
}

```


#### GET /api/v1/order/filled 获取用户成交记录

请求参数:

| 字段      | 备注                         | 是否必须 |
|-----------|------------------------------|----------|
| timestamp | 当前时间戳                   | 是       |
| accessKey | api_key                      | 是       |
| nonce     | 随机字符串                   | 是       |
| signature | 签名                         | 是       |
| limit     | 要几条，[1-200]，不传默认200 | 否       |
| startTime | 开始时间，格式：yyyy-MM-dd   | 否       |
| endTime   | 结束时间，格式：yyyy-MM-dd   | 否       |
| coinPair  | 交易对名称(例:EOS:ETH)       | 是       |
| direction | 交易方向，0买，1卖           | 否       |

响应数据:

| 参数名称 | 描述         | 是否必须 |
|----------|--------------|----------|
| status   | 状态码       | 是       |
| msg      | 响应信息描述 | 是       |
| data     | 响应数据     | 否       |

data说明：

```markdown
"data": [
{
         "id": 成交记录id,
		"coinPair": 交易对名称,
		"price": 成交价格,
		"quantity": 成交数量,
		"time": 成交时间,
		"direction": 成交方向 0买 1卖
	}
]

```


请求响应例子：

```markdown
/*	/api/v1/order/filled?accessKey=eoCmCQCSVg2dtezoZYHBKBh0IySoi3eQ9igzwa2YhuIU2cnI22nksk4LANSbsXcP&endTime=2018-07-06&nonce=kh0m9f&coinPair=EOS/ETH&signature=7573b7281dbd8cd4c8dd186d38122a58&startTime=2018-06-27&timestamp=1532162959223&direction=0	
*/
{
	"status": 1,
	"msg": "操作成功",
	"data": [{
"id": "815315474767366",
		"coinPair": "EOS/ETH",
		"price": "10.000000",
		"quantity": "1.0000",
		"time": 1530032107000,
		"direction": 0
	}
//more data here
]
}
/*	
/api/v1/order/filled?endTime=2018-07-06&nonce=7j4e0v&coinPair=EOS/ETH&signature=e82314dce52556c3270618fa29a6d9bf&startTime=2018-06-27&timestamp=1532163248468&direction=0
*/
{
	"status": 801,
	"msg": "accessKey is not provided"
}

```


### 资产信息

#### GET /api/v1/account/assets 获取用户资产

请求参数

| 字段      | 备注              | 是否必填 |
|-----------|-------------------|----------|
| timestamp | 当前时间戳        | 是       |
| accessKey | api_key           | 是       |
| nonce     | 随机字符串        | 是       |
| signature | 签名              | 是       |
| coin      | 币种名称(例：ETH) | 否       |

响应数据：

| 参数名称 | 描述         | 是否必填 |
|----------|--------------|----------|
| status   | 状态码       | 是       |
| msg      | 响应信息描述 | 是       |
| data     | 响应数据     | 否       |

data说明：

```markdown
"data": [
{
		"coin": 币种简称,
		"available": 账户可用资产,
		"frozen": 账户冻结资产
	}
] 

```

请求响应示例：

```markdown
/*	
/api/v1/account/assets?accessKey=eoCmCQCSVg2dtezoZYHBKBh0IySoi3eQ9igzwa2YhuIU2cnI22nksk4LANSbsXcP&nonce=ok1iie&signature=0264197b2810443b5a29308eddbf3d79&timestamp=1532164460267	
*/
{
	"status": 1,
	"msg": "操作成功",
	"data": [{
		"coin": "ETH",
		"available": "9983.00000000",
		"frozen": "7.00000000"
	}
//more data here
]
}
/*	
/api/v1/account/assets?nonce=e111is&signature=be136277d368109eb7dc01eca3c6f4db&timestamp=1532164551137	
*/
{
	"status": 801,
	"msg": "accessKey is not provided"
}

```


状态码 status
-------------

每次调用接口时，可能获得正确或错误的返回码，开发者可以根据返回码信息调试接口，排查错误。

| 状态码 | 描述                                  |
|--------|---------------------------------------|
| 0      | 网络错误，请稍后重试                  |
| 801    | 缺少accessKey参数                     |
| 802    | accessKey不合法                       |
| 803    | *缺少timestamp参数*                   |
| 804    | *timestamp请求*已超时                 |
| 805    | *缺少nonce参数*                       |
| 806    | *nonce+timestamp*不能重复             |
| 807    | secretKey未配置，请联系客服           |
| 808    | 缺少signature参数                     |
| 809    | signature不匹配                       |
| 810    | *api*功能未授权，请核查接口权限       |
| 811    | 今日签名错误次数已达上限，请明天继续! |
| 812    | 接口请求太频繁                        |
| 813    | *ip*地址已划入黑名单                  |
| 814    | *ip*地址不在白名单中                  |
| 815    | *未绑定域名*                          |
| 816    | *当前域名不允许访问*                  |
| 40034  | 币种暂未开放，敬请期待                |
| 50001  | 金额不足，请充值后交易                |
| 50002  | 交易数量和金额都不能小于0             |
| 50003  | 委托记录不存在                        |
| 50004  | 委托记录状态已经变更                  |
| 60019  | 交易类型不合法                        |
| 60026  | 不合法记录数，正常范围:[1,1000]       |
| 60027  | 不合法的k线类型                       |
| 60028  | 交易对名称不能为空                    |
| 60029  | 不合法记录数，正常范围:[1,200]        |
| 60030  | 委托价格不能为空                      |
| 60031  | 委托数量不能为空                      |
| 60032  | 委托单id不能为空                      |
| 60033  | 当前页码不能为空                      |
| 60034  | 每页条数不能为空                      |
| 60035  | 时间格式错误，正确格式为：yyyy-MM-dd  |
| 60036  | 不合法记录数，正常范围:[1,100]        |
| 60037  | 价格精度不合法，精度为：              |
| 60038  | 数量精度不合法，精度为：              |
| 60039  | 价格格式错误                          |
| 60040  | 数量格式错误                          |
| 91001  | 用户状态异常，请联系管理员            |
