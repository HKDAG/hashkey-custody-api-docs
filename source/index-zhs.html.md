--- 

title: HashKey Prime API Documentation

language_tabs: 
   - shell
   - javascript
   - go
   - java

toc_footers: 
   - <a href='https://github.com/nbltrust/hashkey-custody-api-docs'>Documentation</a>

includes:
  - errors

search: true 

---

# 介绍

Hashkey Prime 提供了一个简单而强大的 RESTful API 和客户端 SDK ，以将数字货币钱包与用户的应用程序集成在一起。欢迎查看我们的 [NodeJs SDK](https://github.com/nbltrust/hashkey-custody-sdk-nodejs), [Go SDK](https://github.com/nbltrust/hashkey-custody-sdk-go), [Java SDK](https://github.com/nbltrust/jadepool-saas-sdk-java)接口。我们提供 2 个级别的 API, 管理API 和 钱包API。

管理 API 可以实现以下功能:

- 创建钱包
- 管理交易所账户中的资产或头寸

钱包 API 可以实现以下功能:

- 钱包余额和交易清单
- 交易创建
- 交易监控和通知

# 环境

HashKey Prime 有两个用于开发和生产的独立环境。 出于安全考虑, 所有API请求均使用TLS加密的HTTPS协议发出。

## 生产环境
生产端点是线上环境，提供给合作伙伴使用。

- 生产网站: https://prime.hashkey.com/
- 生产API端点: https://prime.hashkey.com/saas-api

## 测试环境
测试环境与生产环境完全分开，无论是数据还是账户都没有重叠。您需要在 https://saas.jadepool.io/ 创建账户。 

- 测试网站: https://saas.jadepool.io/
- 测试API端点: https://openapi.jadepool.io

该环境连接到我们支持的各种数字货币的TestNet网络。 这些网络上的代币可以从水龙头上获得，并不代表真实的货币。

# 支持的代币/数字货币
关于生产环境， 请参考文档。
> https://XXXXX

关于测试环境，请参考列表:

| 代币 | 区块链 | 描述 |
| ---- | ---------- | ----------- |
| ETH | Ethereum | Koven testnet |
| NASH | Ethereum | Koven testnet, ERC20 |
| BTC | Bitcoin | public testnet |
| EOS | Eosio | Jungle testnet |
| ATOM | Cosmos | Private testnet |
| IRIS | Irisnet | Private testnet |

# API 认证



# 一般结构

服务器验证请求的一般请求结构。

**参数**

| 名称 | 位置 | 描述 | 是否必需 | 类型 |
| ---- | ---------- | ----------- | -------- | ---- |
| timestamp | query/body | unix timestamp, seconds | Yes | string |
| nonce | query/body | nonce, random string, not repeated in 10 minutes | Yes | string |
| sign | query/body | hex string, sign parameters with HMACSHA256 | Yes | string |

参数 `timestamp`， `nonce` 和 `sign` 位于GET请求的query中, 但位于POST请求的body中。

如果是 POST 请求, body 结构如下:

`
{ 
  "timestamp": 1583376284,
  "nonce": "15833762841261615239762485",
  "mode": "auto",
  "sign":"7042a9fd6deea017be7ad76dfb48e4c36feca279819630c870a628f5352c9044"
}
`

如果是 GET 请求, url 结构如下:

`
/api/v1/app/balance/ETH?timestamp=1583374390&nonce=1583374390314165815853245&sign=7c482cf15d9d25772eee2c43bd146c8136472a055b4587b9fb22d02720037875
`

<font size=4> [签名方式](#signature) 类似于 OAuth.</font>

# 钱包 API

```shell
$ git clone https://github.com/nbltrust/hashkey-custody-sdk-golang.git && cd hashkey-custody-sdk-golang
```

```javascript
const key = 'TyTLvCnHINbWZQag88hhmMz1'
const secret = 'uf0rPlTluGnIllGqx0X1os4hQ6rOdXDxStiN4qGd79lS6yeHZaOK4ldvRv1TBqr6'
const apiAddr = 'http://127.0.0.1:8092'

const api = new API(key, secret, apiAddr)
```

```go
app := sdk.NewAppWithAddr(apiAddr, key, secret)
```

```java
String endpoint = "http://127.0.0.1:8092";
String appKey = "TyTLvCnHINbWZQag88hhmMz1";
String appSecret = "uf0rPlTluGnIllGqx0X1os4hQ6rOdXDxStiN4qGd79lS6yeHZaOK4ldvRv1TBqr6";

APIContext appContext = new APIContext(endpoint, appKey, appSecret);
App appTest = new App(appContext);
```

钱包的 key 和 secret 可在钱包的设置中生成。

出于安全考虑，强烈建议您为 API Key 绑定 IP 地址或 IP 段。

**风险提示：**API Key/Secret 与账号安全紧密相关，无论何时都请勿向其它人透露。API Key/Secret 的泄露可能会造成您的资产损失（即使未开通提现权限），若发现API Key/Secret 泄露请尽快删除该API Key。

## 地址

### 获取最新地址 

```shell
$ go run cmd/ctl/main.go "appkey" "appsecret" "GetAddress" "ETH"
code: 0
message: success
data:
{
  "address": "0x9bf65CDF5729b9588F6bAEBb2Aa2926472D4a035",
  "mode": "deposit"
}
```

```javascript
    try {
        const result = await api.getAddress("ETH")
        console.log(result)
    } catch(e) {
        // do something
        console.log(e.response)
    }
```

```go
	result, _ := app.GetAddress("ETH")
```

```java
  APIResult result = appTest.getAddress("ETH");
```

**总结:** 获取最新地址, 若不存在即创建

#### HTTP请求 
`GET /api/v1/address/{coinName}` 

**参数**

| 名称 | 位置 | 描述 | 是否必需 | 类型 |
| ---- | ---------- | ----------- | -------- | ---- |
| X-App-Key | header | app key | Yes | string |
| coinName | path | coinName | Yes | string |

**响应结果**

值 | 类型 | 描述
--------- | ------- | ---------
address | string | the new address
mode | string | address mode

### 创建新地址 

```shell
$ go run cmd/ctl/main.go "appkey" "appsecret" "CreateAddress" "ETH"
code: 0
message: success
data:
{
  "address": "0x9bf65CDF5729b9588F6bAEBb2Aa2926472D4a035",
  "mode": "deposit"
}
```

```javascript
    try {
        const result = await api.createAddress("ETH")
        console.log(result)
    } catch(e) {
        // do something
        console.log(e.response)
    }
```

```go
	result, _ := app.CreateAddress("ETH")
```

```java
  APIResult result = appTest.createAddress("ETH");
```

**总结:** 创建新地址

#### HTTP请求
`POST /api/v1/address/{coinName}/new` 

**参数**

| 名称 | 位置 | 描述| 是否必需| 类型 |
| ---- | ---------- | ----------- | -------- | ---- |
| X-App-Key | header | app key | Yes | string |
| coinName | path | coinName | Yes | string |
| mode | body | mode | No | string |

**响应结果**

值 | 类型 | 描述
--------- | ------- | ---------
address | string | the new address
mode | string | address mode

### 验证地址

```shell
$ go run cmd/ctl/main.go "appkey" "appsecret" "VerifyAddress" "ETH" "0x9bf65CDF5729b9588F6bAEBb2Aa2926472D4a035"
code: 0
message: success
data:
{
  "address": "0x9bf65CDF5729b9588F6bAEBb2Aa2926472D4a035",
  "valid": true
}
```

```javascript
    try {
        result = await api.verifyAddress("ETH", address) 
        console.log(result)
    } catch(e) {
        // do something
        console.log(e)
    }
```

```go
	result, _ = app.VerifyAddress("ETH", "0x9bf65CDF5729b9588F6bAEBb2Aa2926472D4a035")
```

```java
  APIResult result = appTest.verifyAddress("ETH", "0x9bf65CDF5729b9588F6bAEBb2Aa2926472D4a035");
```

**总结:** 验证地址

#### HTTP请求 
`POST /api/v1/address/{coinName}/verify` 

**参数**

| 名称 | 位置 | 描述| 是否必需| 类型 |
| ---- | ---------- | ----------- | -------- | ---- |
| X-App-Key | header | app key | Yes | string |
| coinName | path | coinName | Yes | string |
| address | body | address | Yes | string |

**响应结果**

值 | 类型 | 描述
--------- | ------- | ---------
address | string | the address to validate
valid | boolean | the address is valid or not

## 钱包
### 获取所有可用资产

```shell
$ go run cmd/ctl/main.go "appkey" "appsecret" "GetAllAssets"
code: 0
message: success
data:
{
  "assets": [
    "ETH"
  ]
}
```

```javascript
    try {
        result = await api.getAllAssets()
        console.log(result)
    } catch(e) {
        // do something
        console.log(e)
    }
```

```go
	result, _ = app.GetAllAssets()
```

```java
  APIResult result = appTest.getAllAssets();
```

**总结:** 获取所有可用资产

#### HTTP请求 
`GET /api/v1/app/allAssets` 

**参数**

| 名称 | 位置 | 描述| 是否必需| 类型 |
| ---- | ---------- | ----------- | -------- | ---- |
| X-App-Key | header | app key | Yes | string |

**响应结果**

值 | 类型 | 描述
--------- | ------- | ---------
assets | array | the wallet coin list

### 获取钱包余额

```shell
$ go run cmd/ctl/main.go "appkey" "appsecret" "GetBalances"
code: 0
message: success
data:
{
  "balances": [
    {
      "balance": "10001.225",
      "money": "98175466.911631525",
      "name": "BTC",
      "price": "9816.344189"
    },
    {
      "balance": "1.0",
      "money": "246.565827",
      "name": "ETH",
      "price": "246.565827"
    }
  ],
  "total": "98175713.477458525"
}
```

```javascript
    try {
        result = await api.getBalances()
        console.log(result)
    } catch(e) {
        // do something
        console.log(e)
    }
```

```go
	result, _ = app.GetBalances()
```

```java
  APIResult result = appTest.getBalances();
```

**总结:** 获取所有资产余额， 包括以美元计算的数量和价格

#### HTTP请求 
`GET /api/v1/app/balances` 

**参数**

| 名称 | 位置 | 描述| 是否必需| 类型 |
| ---- | ---------- | ----------- | -------- | ---- |
| X-App-Key | header | app key | Yes | string |

**响应结果**

值 | 类型 | 描述
--------- | ------- | ---------
total | string | total money(USD)
balances | array | the wallet balance list

余额:

值 | 类型 | 描述
--------- | ------- | ---------
balance | string | the coin balance
money | string | the amount(USD) equal to the balance
name | string | the coin name
price | string | the coin price(USD)

### 添加钱包资产

```shell
$ go run cmd/ctl/main.go "appkey" "appsecret" "AddAsset" "BTC"
code: 0
message: success
data:
{}
```

```javascript
    try {
        result = await api.addAppAsset("BTC")
        console.log(result)
    } catch(e) {
        // do something
        console.log(e)
    }
```

```go
	result, _ = app.AddAsset("BTC")
```

```java
  APIResult result = appTest.addAppAsset("BTC");
```

**总结:** 添加钱包资产

#### HTTP请求 
`POST /api/v1/app/assets` 

**参数**

| 名称 | 位置 | 描述| 是否必需| 类型 |
| ---- | ---------- | ----------- | -------- | ---- |
| X-App-Key | header | app key | Yes | string |
| coinName | body | asset name | Yes | array |

**响应结果**

值 | 类型 | 描述
--------- | ------- | ---------

### 获取钱包余额

```shell
$ go run cmd/ctl/main.go "appkey" "appsecret" "GetBalance" "ETH"
code: 0
message: success
data:
{
  "balance": "0.450000000000000000",
  "inLocked": "0.000000000000000000",
  "inLockedFee": "0.000000000000000000",
  "outLocked": "0.000000000000000000",
  "outLockedFee": "0.000000000000000000",
  "stakingBalance": "0.000000000000000000",
  "stakingInLocked": "0.000000000000000000",
  "stakingOutLocked": "0.000000000000000000",
  "delegateAmount": "0.000000000000000000",
  "delegateInLocked": "0.000000000000000000",
  "delegateOutLocked": "0.000000000000000000",
  "undelegateAmount": "0.000000000000000000",
  "undelegateInLocked": "0.000000000000000000",
  "undelegateOutLocked": "0.000000000000000000"
}
```

```javascript
    try {
        result = await api.getBalance("ETH")
        console.log(result)
    } catch(e) {
        // do something
        console.log(e)
    }
```

```go
	result, _ = app.GetBalance("ETH")
```

```java
  APIResult result = appTest.getBalance("ETH");
```

**总结:** 获取钱包余额

#### HTTP请求 
`GET /api/v1/app/balance/{coinName}` 

**参数**

| 名称 | 位置 | 描述| 是否必需| 类型 |
| ---- | ---------- | ----------- | -------- | ---- |
| X-App-Key | header | app key | Yes | string |
| coinName | path | coin type | Yes | string |

**响应结果**

值 | 类型 | 描述
--------- | ------- | ---------
balance | string | the coin balance
inLocked | string | the coin transfer in amount under locked
inLockedFee | string | the coin transfer in fee under locked
outLocked | string | the coin transfer out amount under locked
outLockedFee | string | the coin transfer out fee under locked
stakingBalance | string | the staking coin balance
stakingInLocked | string | the staking coin transfer in amount under locked
stakingOutLocked | string | the staking coin transfer out amount under locked
delegateAmount | string | the coin delegate amount
delegateInLocked | string | the coin delegate transfer in amount under locked
delegateOutLocked | string | the coin delegate transfer out amount under locked
undelegateAmount | string | the undelegate coin amount
undelegateInLocked | string | the undelegate coin transfer in amount under locked
undelegateOutLocked | string | the undelegate coin transfer out amount under locked

### 获取钱包资产

```shell
$ go run cmd/ctl/main.go "appkey" "appsecret" "GetAssets"
code: 0
message: success
data:
{
  "assets": [
    "ETH"
  ]
}
```

```javascript
    try {
        result = await api.getAssets()
        console.log(result)
    } catch(e) {
        // do something
        console.log(e)
    }
```

```go
	result, _ = app.GetAssets()
```

```java
  APIResult result = appTest.getAssets();
```

**总结:** 获取钱包资产

#### HTTP请求 
`GET /api/v1/app/assets` 

**参数**

| 名称 | 位置 | 描述| 是否必需| 类型 |
| ---- | ---------- | ----------- | -------- | ---- |
| X-App-Key | header | app key | Yes | string |

**响应结果**

值 | 类型 | 描述
--------- | ------- | ---------
assets | array | the wallet coin list

### 通过id获取钱包资产

```shell
$ go run cmd/ctl/main.go "appkey" "appsecret" "GetAssets"
code: 0
message: success
data:
{
  "assets": [
    {
      "id": 1,
      "name": "ETH",
      "absFee": "0.005",
      "canDeposit": true,
      "canWithdraw": true,
      "decimal": 18,
      "description": "ETH",
      "feeCoin": "ETH",
      "priorityAverage": {
        "fee": "0.02",
        "priority": "4.0"
      },
      "priorityFast": {
        "fee": "0.025",
        "priority": "5.0"
      },
      "prioritySlow": {
        "fee": "0.01",
        "priority": "2.0"
      },
      "priorityVeryFast": {
        "fee": "0.03",
        "priority": "6.0"
      },
      "priorityVerySlow": {
        "fee": "0.015",
        "priority": "3.0"
      }
    }
  ]
}
```

**总结:** 通过id获取钱包资产

#### HTTP请求 
`GET /api/v1/app/assetsWithID`

**参数**

| 名称 | 位置 | 描述| 是否必需| 类型 |
| ---- | ---------- | ----------- | -------- | ---- |
| X-App-Key | header | app key | Yes | string |

**响应结果**

值 | 类型 | 描述
--------- | ------- | ---------
assets | array | the wallet coin list

资产:

值 | 类型 | 描述
--------- | ------- | ---------
id | number | the asset id
name | string | the asset name
absFee | string | withdraw fee
canDeposit | boolean | whether or not the asset can be deposited
canWithdraw | boolean | whether or not the asset can be withdrawn
decimal | number | the asset decimal
description | string | the asset description
feeCoin | string | withdraw fee coin
priorityAverage | object | withdraw fee with average priority
priorityFast | object | withdraw fee with fast priority
prioritySlow | object | withdraw fee with slow priority
priorityVeryFast | object | withdraw fee with very fast priority
priorityVerySlow | object | withdraw fee with very slow priority

优先级:

值 | 类型 | 描述
--------- | ------- | ---------
fee | string | fee with the priority
priority | string | the priority, set into the withdraw request

### 获取钱包订单

```shell
$ go run cmd/ctl/main.go "appkey" "appsecret" "GetOrders" 1 10
code: 0
message: success
data:
{
  "totalAmount": 1,
  "orders": [{
    "bizType": "WITHDRAW",
    "block": 12970670,
    "coinName": "ETH",
    "confirmations": 21,
    "fee": "0.005000000000000000",
    "from": "0xdedc1eca923cc1227c20571030146d8a01b70774",
    "id": "rNXBQGJlw09apVyg4nDo",
    "memo": "",
    "n": 0,
    "state": "DONE",
    "to": "0xF0706B7Cab38EA42538f4D8C279B6F57ad1d4072",
    "txid": "0xcfc4cb925c77c2ea10be3c233029fc1f05d0ddffe7396920ddd493544e52933d",
    "type": "ETH",
    "value": "0.045000000000000000"
  }]
}
```

```javascript
    try {
        result = await api.getOrders(1, 10)
        console.log(result)
    } catch(e) {
        // do something
        console.log(e)
    }
```

```go
	result, _ = app.GetOrders(1, 10)
```

```java
  APIResult result = appTest.getOrders(1, 10);
```

**总结:** 获取钱包订单

#### HTTP请求 
`GET /api/v1/app/orders` 

**参数**

| 名称 | 位置 | 描述| 是否必需| 类型 |
| ---- | ---------- | ----------- | -------- | ---- |
| X-App-Key | header | app key | Yes | string |
| page | query | page, e.g. 1 | Yes | string |
| amount | query | item count on this page, e.g. 10 | Yes | string |
| coins | query | coin list, can be empty string | No | string |
| state | query | order state, can be empty string | No | string |
| bizType | query | order type, TRANSFER_IN/TRANSFER_OUT/WITHDRAW/DEPOSIT | No | string |

**响应结果**

值 | 类型 | 描述
--------- | ------- | ---------
orders | array | order list
totalAmount | number | the total count of orders

### 获取钱包订单

```shell
$ go run cmd/ctl/main.go "appkey" "appsecret" "GetOrder" "rNXBQGJlw09apVyg4nDo"
code: 0
message: success
data:
{
  "bizType": "WITHDRAW",
  "block": 12970670,
  "coinName": "ETH",
  "confirmations": 21,
  "fee": "0.005000000000000000",
  "from": "0xdedc1eca923cc1227c20571030146d8a01b70774",
  "id": "rNXBQGJlw09apVyg4nDo",
  "memo": "",
  "n": 0,
  "state": "DONE",
  "to": "0xF0706B7Cab38EA42538f4D8C279B6F57ad1d4072",
  "txid": "0xcfc4cb925c77c2ea10be3c233029fc1f05d0ddffe7396920ddd493544e52933d",
  "type": "ETH",
  "value": "0.045000000000000000",
  "note": "note",
  "message": ""
}
```

```javascript
    try {
        result = await api.getOrder("rNXBQGJlw09apVyg4nDo")
        console.log(result)
    } catch(e) {
        // do something
        console.log(e)
    }
```

```go
	result, _ = app.GetOrder("rNXBQGJlw09apVyg4nDo")
```

```java
  APIResult result = appTest.getOrder("rNXBQGJlw09apVyg4nDo");
```

**总结:** 获取钱包订单

#### HTTP请求 
`GET /api/v1/app/order/{id}` 

**参数**

| 名称 | 位置 | 描述| 是否必需| 类型 |
| ---- | ---------- | ----------- | -------- | ---- |
| X-App-Key | header | app key | Yes | string |
| id | path | order id | Yes | string |

**响应结果**

值 | 类型 | 描述
--------- | ------- | ---------
bizType | string | order type
block | number | the block transaction mined in
coinName | string | unique token name
confirmations | number | number of transaction confirmations
fee | string | fee burnt for the transaction
from | string | transaction input
id | string | order id
memo | string | order memo
n | number | order index
state | string | order state
to | string | transaction output
txid | string | transaction hash
type | string | token type
value | string | transaction value
note | string | order note
message | string | message to recipient of transfer

### 更新钱包订单

```shell
$ go run cmd/ctl/main.go "appkey" "appsecret" "UpdateOrder" "rNXBQGJlw09apVyg4nDo" "123"
code: 0
message: success
data:
{}
```

```go
	result, _ = app.UpdateOrder("rNXBQGJlw09apVyg4nDo", "123")
```

**总结:** 更新钱包订单

#### HTTP请求 
`PUT /api/v1/app/order/{id}` 

**参数**

| 名称 | 位置 | 描述| 是否必需| 类型 |
| ---- | ---------- | ----------- | -------- | ---- |
| X-App-Key | header | app key | Yes | string |
| id | path | order id | Yes | string |
| note | body | note | No | string |

**响应结果**

值 | 类型 | 描述
--------- | ------- | ---------

### 提现

```shell
$ go run cmd/ctl/main.go "appkey" "appsecret" "Withdraw" "$(date +%s)" "ETH" "0xF0706B7Cab38EA42538f4D8C279B6F57ad1d4072" "0.05"
code: 0
message: success
data:
{
  "bizType": "WITHDRAW",
  "block": -1,
  "coinName": "ETH",
  "confirmations": 0,
  "fee": "0.0050000000",
  "from": "",
  "id": "Jd7qbDRa1qM1lxK5Qe2M",
  "memo": "",
  "n": 0,
  "state": "INIT",
  "to": "0xF0706B7Cab38EA42538f4D8C279B6F57ad1d4072",
  "txid": "",
  "type": "ETH",
  "value": "0.0450000000"
}
```

```javascript
    try {
        let id = ''+new Date().valueOf()
        let coinType = 'ETH'
        let to = '0x56204b988844b20160035273fD98Dbb2A54C85F5'
        let value = '0.01'
        let memo = ''
        result = await api.withdraw(id, coinType, to, value, memo)
        console.log(result)
    } catch(e) {
        // do something
        console.log(e)
    }
```

```go
	result, _ = app.Withdraw("1569225735", "ETH", "0xF0706B7Cab38EA42538f4D8C279B6F57ad1d4072", "0.05")
```

```java
  APIResult result = appTest.withdraw(id, "ETH", "0xF0706B7Cab38EA42538f4D8C279B6F57ad1d4072", "0.05");
```

**总结:** 提现

#### HTTP请求 
`POST /api/v1/app/{coinName}/withdraw` 

**参数**

| 名称 | 位置 | 描述| 是否必需| 类型 |
| ---- | ---------- | ----------- | -------- | ---- |
| X-App-Key | header | app key | Yes | string |
| coinName | path | coin type | Yes | string |
| id | body | withdraw id | Yes | string |
| to | body | receive address | Yes | string |
| value | body | withdraw amount | Yes | string |
| memo | body | address memo | No | string |
| note | body | note | No | string |
| priority | body | priority | No | string |

**响应结果**

值 | 类型 | 描述
--------- | ------- | ---------
bizType | string | order type
block | number | the block transaction mined in
coinName | string | unique token name
confirmations | number | number of transaction confirmations
fee | string | fee burnt for the transaction
from | string | transaction input
id | string | order id
memo | string | address memo
n | number | order index
state | string | order state
to | string | transaction output
txid | string | transaction hash
type | string | token type
value | string | transaction value
note | string | order note

### 划转

```shell
$ go run cmd/ctl/main.go "appkey" "appsecret" Transfer "e5dJyVp8R3B1m4o" "ETH" "0.01"
code: 0
message: success
data:
{
  "bizType": "TRANSFER_OUT",
  "coinName": "ETH",
  "confirmations": 0,
  "fee": "0",
  "from": "L6RayqPn4jXExW0",
  "id": "orders4eq8kz1235jz2yj0n9rvpwl7",
  "memo": "",
  "message": "",
  "n": 0,
  "note": "",
  "state": "DONE",
  "to": "e5dJyVp8R3B1m4o",
  "txid": "",
  "type": "ETH",
  "value": "0.01"
}
```

```go
	result, _ = app.Transfer("e5dJyVp8R3B1m4o", "ETH", "0.01")
```

**总结:** 划转

#### HTTP请求 
`POST /api/v1/app/{coinName}/transfer` 

**参数**

| 名称 | 位置 | 描述| 是否必需| 类型 |
| ---- | ---------- | ----------- | -------- | ---- |
| X-App-Key | header | app key | Yes | string |
| coinName | path | coin type | Yes | string |
| to | body | receive wallet id | Yes | string |
| value | body | transfer amount | Yes | string |
| note | body | transfer note, can be empty string | Yes | string |
| message | body | transfer message, can be empty string | Yes | string |

**响应结果**

值 | 类型 | 描述
--------- | ------- | ---------
bizType | string | order type
block | number | the block transaction mined in
coinName | string | unique token name
confirmations | number | number of transaction confirmations
fee | string | fee burnt for the transaction
from | string | transaction input
id | string | order id
memo | string | order note
n | number | order index
state | string | order state
to | string | transaction output
txid | string | transaction hash
type | string | token type
value | string | transaction value
note | string | transfer note
message | string | transfer message

## 质押
### 获取质押validators 

```shell
$ go run cmd/ctl/main.go "appkey" "appsecret" "GetValidators" "IRIS"
code: 0
message: success
data:
{
  "validators": [
    {
      "address": "fva125aw6ew57jugyultpcqrlk7sewamxtjer48cy0",
      "commission": {
        "maxChangeRate": 1,
        "maxRate": 1,
        "rate": 0.1,
        "updateAt": "0001-01-01T00:00:00.000Z"
      },
      "delegationAmount": "2770068.5544012594476618430000000000",
      "jailed": false,
      "minSelfDelegation": "0",
      "name": "validator0",
      "pubkey": "fcp1zcjduepqt9c5p8e25s5zxxsg5758cx4aqylarkxrc35qg0d0pge84p3rtaasmz58sl",
      "shares": "2770068.5544012594476618430000000000",
      "status": 2
    },
    {
      "address": "fva1at9ye95xmsp9wzpc6asmju5w55nece5c4fv28m",
      "commission": {
        "maxChangeRate": 1,
        "maxRate": 1,
        "rate": 0.1,
        "updateAt": "0001-01-01T00:00:00.000Z"
      },
      "delegationAmount": "370.1929307965746291140000000000",
      "jailed": true,
      "minSelfDelegation": "0",
      "name": "validator1",
      "pubkey": "fcp1zcjduepqj0gp48fu32h2ttsn4anz368cyjyklq0ut7c0slk0ncxqv9l3mqmsehkpr6",
      "shares": "370.1929307965746291140000000000",
      "status": 0
    }
  ]
}
```

```javascript
    try {
        result = await api.getValidators("IRIS")
        console.log(result)
    } catch(e) {
        // do something
        console.log(e)
    }
```

```go
	result, _ = app.GetValidators("IRIS")
```

```java
  APIResult result = appTest.getValidators("IRIS");
```

**总结:** 获取质押validators

#### HTTP请求 
`GET /api/v1/staking/{coinName}/validators` 

**参数**

| 名称 | 位置 | 描述| 是否必需| 类型 |
| ---- | ---------- | ----------- | -------- | ---- |
| X-App-Key | header | app key | Yes | string |
| coinName | path | coin type | Yes | string |

**响应结果**

值 | 类型 | 描述
--------- | ------- | ---------
validators | array | the validator list

### 抵押

```shell
$ go run cmd/ctl/main.go "appkey" "appsecret" "Delegate" $(date +%s) "IRIS2" "1"
code: 0
message: success
data:
{
  "id": "J9rPWXA13Kl9bam4Vo6G",
  "type": "DELEGATE",
  "value": "1"
}
```

```javascript
    try {
        let id = ''+new Date().valueOf()
        let coinType = 'ATOM'
        let value = '0.06'
        result = await api.delegate(id, coinType, value)
        console.log(result)
    } catch(e) {
        // do something
        console.log(e)
    }
```

```go
	result, _ = app.Delegate("1569231558", "IRIS2", "1")
```

```java
  APIResult result = appTest.delegate("1569231558", "IRIS2", "1");
```

**总结:** 抵押

#### HTTP请求 
`POST /api/v1/staking/{coinName}/delegate` 

**参数**

| 名称 | 位置 | 描述| 是否必需| 类型 |
| ---- | ---------- | ----------- | -------- | ---- |
| X-App-Key | header | app key | Yes | string |
| coinName | path | coin type | Yes | string |
| id | body | delegate custom order id | Yes | string |
| value | body | delegate amount | Yes | string |

**响应结果**

值 | 类型 | 描述
--------- | ------- | ---------
id | string | order id
type | string | order type
value | string | delegate amount

### 解抵押

```shell
$ go run cmd/ctl/main.go "appkey" "appsecret" "UnDelegate" $(date +%s) "IRIS2" "1"
code: 0
message: success
data:
{
  "id": "P6wAQNrWp5XanXk5L7VR",
  "type": "UNDELEGATE",
  "value": "1"
}
```

```javascript
    try {
        let id = ''+new Date().valueOf()
        let coinType = 'ATOM'
        let value = '0.01'
        result = await api.undelegate(id, coinType, value)
        console.log(result)
    } catch(e) {
        // do something
        console.log(e)
    }
```

```go
	result, _ = app.UnDelegate("1569231809", "IRIS2", "1")
```

```java
  APIResult result = appTest.unDelegate("1569231809", "IRIS2", "1");
```

**总结:** 解抵押

#### HTTP请求 
`POST /api/v1/staking/{coinName}/undelegate` 

**参数**

| 名称 | 位置 | 描述| 是否必需| 类型 |
| ---- | ---------- | ----------- | -------- | ---- |
| X-App-Key | header | app key | Yes | string |
| coinName | path | coin type | Yes | string |
| id | body | delegate custom order id | Yes | string |
| value | body | delegate amount | Yes | string |

**响应结果**

值 | 类型 | 描述
--------- | ------- | ---------
id | string | order id
type | string | order type
value | string | delegate amount

### 添加资金需求

```shell
$ go run cmd/ctl/main.go "appkey" "appsecret" "AddUrgentStakingFunding" $(date +%s) "IRIS2" "1" "1569302076"
code: 0
message: success
data:
{}
```

```javascript
    try {
        let id = ''+new Date().valueOf()
        let coinType = 'ATOM'
        let value = '0.06'
        let expiredAt = parseInt(new Date().valueOf()/1000) + 86400 * 21
        result = await api.addUrgentStakingFunding(id, coinType, value, expiredAt)
        console.log(result)
    } catch(e) {
        // do something
        console.log(e)
    }
```

```go
	result, _ = app.AddUrgentStakingFunding("1569292076", "IRIS2", "1", 1569302076)
```

```java
  APIResult result = appTest.addUrgentStakingFunding("1569292076", "IRIS2", "1", 1569302076);
```

**总结:** 添加资金需求

#### HTTP请求 
`POST /api/v1/staking/{coinName}/funding` 

**参数**

| 名称 | 位置 | 描述| 是否必需| 类型 |
| ---- | ---------- | ----------- | -------- | ---- |
| X-App-Key | header | app key | Yes | string |
| coinName | path | coin type | Yes | string |
| id | body | funding id | Yes | string |
| value | body | funding amount | Yes | string |
| expiredAt | body | expired time | Yes | number |

**响应结果**

值 | 类型 | 描述
--------- | ------- | ---------

### 获取质押利息

```shell
$ go run cmd/ctl/main.go "appkey" "appsecret" "GetStakingInterest" "IRIS" "2019-09-26"
code: 0
message: success
data:
{
  "coinName": "IRIS",
  "interest": "0.12345"
}
```

```javascript
    try {
        result = await api.getStakingInterest("IRIS", "2019-09-26")
        console.log(result)
    } catch(e) {
        // do something
        console.log(e)
    }
```

```go
	result, _ = app.GetStakingInterest("IRIS", "2019-09-26")
```

```java
  APIResult result = appTest.getStakingInterest("IRIS", "2019-09-26");
```

**总结:** 获取质押利息

#### HTTP请求 
`GET /api/v1/staking/{coinName}/interest` 

**参数**

| 名称 | 位置 | 描述| 是否必需| 类型 |
| ---- | ---------- | ----------- | -------- | ---- |
| X-App-Key | header | app key | Yes | string |
| coinName | path | coin type | Yes | string |
| date | query | UTC date | Yes | string |

**响应结果**

值 | 类型 | 描述
--------- | ------- | ---------
coinName | string | the coin name
value | string | the interest value

## 市场

### 当前价格

```shell
$ go run cmd/ctl/main.go "appkey" "appsecret" "GetMarket" "BTC"
code: 0
message: success
data:
{
  "price": "10000"
}
```

```java
  APIResult result = appTest.getMarket("ETH");
```

**总结:** 获取以USD计价的资产价格

#### HTTP请求 
`GET /api/v1/market/{coinName}`

**参数**

| 名称 | 位置 | 描述| 是否必需| 类型 |
| ---- | ---------- | ----------- | -------- | ---- |
| X-App-Key | header | app key | Yes | string |
| coinName  | path | asset name | Yes | string |

**响应结果**

值 | 类型 | 描述
--------- | ------- | ---------
price | string | current price

## 系统

### 当前时间戳

```shell
$ go run cmd/ctl/main.go "appkey" "appsecret" "SystemGetTime"
code: 0
message: success
data:
{
  "timestamp": 1590391287
}
```

**总结:** 获取系统当前时间戳

#### HTTP请求 
`GET /api/v1/system/time`

**参数**

| 名称 | 位置 | 描述| 是否必需| 类型 |
| ---- | ---------- | ----------- | -------- | ---- |
| X-App-Key | header | app key | Yes | string |

**响应结果**

值 | 类型 | 描述
--------- | ------- | ---------
timestamp | number | current timestamp

# 企业 API

```shell
$ git clone https://github.com/nbltrust/hashkey-custody-sdk-golang.git && cd hashkey-custody-sdk-golang
```

```javascript
const companyKey = 'TyTLvCnHINbWZQag88hhmMz1'
const companySecret = 'uf0rPlTluGnIllGqx0X1os4hQ6rOdXDxStiN4qGd79lS6yeHZaOK4ldvRv1TBqr6'
const apiAddr = 'http://127.0.0.1:8092'

const api = new API(companyKey, companySecret, apiAddr)
```

```go
company := sdk.NewCompanyWithAddr(apiAddr, key, secret)
```

```java
String endpoint = "http://127.0.0.1:8092";
String appKey = "TyTLvCnHINbWZQag88hhmMz1";
String appSecret = "uf0rPlTluGnIllGqx0X1os4hQ6rOdXDxStiN4qGd79lS6yeHZaOK4ldvRv1TBqr6";
APIContext companyContext = new APIContext(endpoint, appKey, appSecret);
Company companyTest = new Company(companyContext);
```

企业 key 和 secret 可在企业设置中生成。

出于安全考虑，强烈建议您为企业 API Key 绑定  IP 地址或 IP 段。

**风险提示：**API Key/Secret 与账号安全紧密相关，无论何时都请勿向其它人透露。若发现API Key/Secret 泄露请尽快删除该API Key。

## 钱包
### 创建

```shell
$ go run cmd/ctl/main.go "companykey" "companysecret" "CreateWallet" "name" "password" "https://noti.domain/callback"
code: 0
message: success
data:
{
  "id": "ylr07eg5p862mkpw",
  "appKey": "52u036rjmnd5qtwc74vohpj3",
  "appSecret": "4d6718b405cb55e5544ba0b343472acba8a5a1eb0c5777e4040b38f9edd96a60",
  "encryptedAppSecret": "7p128MoVWgSFI7AYAUXO9Px72qCNYSvtM5PCAGD3f6de+HjYJbJBEJcTswMH1qdLcKIriuy1RoP6P0YmNeEDlAfSSyJiHrX98Oid5/fuCEs="
}
```

```go
	result, _ = company.CreateWallet("name", "password", "https://noti.domain/callback")
```

```java
  APIResult result = companyTest.createWallet("name", "password", "https://noti.domain/callback", "description");
```

**总结:** 创建钱包

#### HTTP请求 
`POST /api/v1/app` 

**参数**

| 名称 | 位置 | 描述| 是否必需| 类型 |
| ---- | ---------- | ----------- | -------- | ---- |
| X-Company-Key | header | company key | Yes | string |
| name | body | the wallet name | Yes | string |
| password  | body | the wallet password, should encrypted by [AES encryption](#aes-encryption) and base64 encoded | Yes | string |
| description | body | the wallet description | Yes | string |
| webHook | body | the url will be called when send [a notification](#callback) | Yes | string |
| aesIV | body | base64 encoded [AES encryption](#aes-encryption) iv, must be 16 bytes, used by encrypting app password and secret | Yes | string |

**响应结果**

值 | 类型 | 描述
--------- | ------- | ---------
id | string | wallet id
appKey | string | the appkey placed in the header of [Wallet API](#wallet-api)
encryptedAppSecret | string | the base64 encoded [AES encrypted](#aes-encryption) appsecret, the appsecret used for the [signature](#signature) of [Wallet API](#wallet-api)

### 获取appkeys

```shell
$ go run cmd/ctl/main.go "companykey" "companysecret" "GetWalletKeys" "appID"
code: 0
message: success
data:
{
  "keys": [
    {
      "appKey": "nq73yzkovqo44fsek2nq37nw",
      "appSecret": "nn2oh58iuc1sg2adya1golz35cowjbz7r0hfjt2ey6b4fc4xyq6kdopp9tlp0ko3",
      "enable": true,
      "encryptedAppSecret": "+XowuN5bDYKKoDXvci19uoOiYAR+esV762cnBFCd2oX5pfRvOsCsRCyS0eopj9TdziDCm2N8YIBk+/Gj0JGwwKjtXA829ivYdiiVHEe6jvE="
    }
  ]
}
```

```go
	result, _ = company.GetWalletKeys("appID")
```

```java
  APIResult result = companyTest.getWalletKeys("appID");
```

**总结:** 获取 appkeys(包括 appsecret)

#### HTTP请求 
`GET /api/v1/app/{appID}/keys` 

**参数**

| 名称 | 位置 | 描述| 是否必需| 类型 |
| ---- | ---------- | ----------- | -------- | ---- |
| X-Company-Key | header | company key | Yes | string |
| appID | path | wallet id | Yes | string |
| aesIV | query | base64 encoded [AES encryption](#aes-encryption) iv, must be 16 bytes, used by encrypting app secret in the response | Yes | string |

**响应结果**

值 | 类型 | 描述
--------- | ------- | ---------
keys | array | the appkey list

key:

值 | 类型 | 描述
--------- | ------- | ---------
appKey | string | the appkey placed in the header of [Wallet API](#wallet-api)
enable | boolean | enable flag, the appkey will been forbidden if set false
encryptedAppSecret | string | the base64 encoded [AES encrypted](#aes-encryption) appsecret, the appsecret used for the [signature](#signature) of [Wallet API](#wallet-api)

### 更新 appkey

```shell
$ go run cmd/ctl/main.go "companykey" "companysecret" UpdateWalletKey "appKey" true
code: 0
message: success
data:
{}
```

```go
	result, _ = company.UpdateWalletKey("appKey", true)
```

```java
  APIResult result = companyTest.updateWalletKey("appKey", true);
```

**总结:** 更新appkey属性

#### HTTP请求 
`PUT /api/v1/appKey/{appKey}` 

**参数**

| 名称 | 位置 | 描述| 是否必需| 类型 |
| ---- | ---------- | ----------- | -------- | ---- |
| X-Company-Key | header | company key | Yes | string |
| appKey | path | wallet appkey | Yes | string |
| enable | body | enable flag, the appkey will been forbidden if set false | Yes | boolean |

**响应结果**

值 | 类型 | 描述
--------- | ------- | ---------


## Trading
### 获取交易所列表

```javascript
    try {
        result = await api.getExchanges()
        console.dir(result, {depth: null})
    } catch(e) {
        // do something
        console.log(e)
    }
```

**总结:** 获取交易所列表

#### HTTP请求 
`GET /api/v1/exchanges` 

**参数**

| 名称 | 位置 | 描述| 是否必需| 类型 |
| ---- | ---------- | ----------- | -------- | ---- |
| X-Company-Key | header | company key | Yes | string |

**响应结果**

值 | 类型 | 描述
--------- | ------- | ---------
exchanges | array | exchanges list

### 获取交易所余额

```javascript
    try {
        result = await api.getExchangeBalances("z41mwygl0gnl2keo")
        console.dir(result, {depth: null})
    } catch(e) {
        // do something
        console.log(e)
    }
```

**总结:** 获取交易所余额

#### HTTP请求 
`GET /api/v1/exchange/balances` 

**参数**

| 名称 | 位置 | 描述| 是否必需| 类型 |
| ---- | ---------- | ----------- | -------- | ---- |
| X-Company-Key | header | company key | Yes | string |
| exchangeID | query | exchange id | Yes | string |

**响应结果**

值 | 类型 | 描述
--------- | ------- | ---------
balances | array | balances list

### 交易所划转 

```javascript
    try {
        result = await tradingTransfer("WALLET", "rKyOD9pAJPAg54e", "EXCHANGE", "qdkrm8621j73zx4y", "USDT", "2.73", "wallet to binance")
        console.log(result)
    } catch(e) {
        // do something
        console.log(e)
    }
```

**总结:** 交易所划转

#### HTTP请求 
`POST /api/v1/exchange/transfer` 

**参数**

| 名称 | 位置 | 描述| 是否必需| 类型 |
| ---- | ---------- | ----------- | -------- | ---- |
| X-Company-Key | header | company key | Yes | string |
| fromType | body | from type: enum[EXCHANGE,WALLET] | Yes | string |
| fromID | body | from id | Yes | string |
| toType | body | to type: enum[EXCHANGE,WALLET] | Yes | string |
| toID | body | to id | Yes | string |
| amount | body | transfer amount | Yes | string |
| assetName | body | coin name | Yes | string |
| remark | body | remark | No | string |

**响应结果**

值 | 类型 | 描述
--------- | ------- | ---------
withdrawID | string | withdraw order id
amount | string | real transfer amount
fee | string | exchange fee amount
status | string | withdraw order status, enum[PENDING,FAILED,DONE]

### 获取交易所记录列表 

```javascript
    try {
        result = await getExchangeFundings("z41mwygl0gnl2keo", 1)
        console.log(result)
    } catch(e) {
        // do something
        console.log(e)
    }
```

**总结:** 获取交易所记录

#### HTTP请求 
`GET /api/v1/exchange/funding/records` 

**参数**

| 名称 | 位置 | 描述| 是否必需| 类型 |
| ---- | ---------- | ----------- | -------- | ---- |
| X-Company-Key | header | company key | Yes | string |
| exchangeID | query | exchange id | Yes | string |
| page | query | page, e.g. 1, 50 items per page | Yes | string |

**响应结果**

值 | 类型 | 描述
--------- | ------- | ---------
totalAmount| number |  total record number
records | array | record list

### 获取交易所记录 

```javascript
    try {
        result = await getExchangeFunding("3qky8xo2l0gpdg7erpmdwzn1")
        console.log(result)
    } catch(e) {
        // do something
        console.log(e)
    }
```

**总结:** 获取交易所记录

#### HTTP请求 
`GET /api/v1/exchange/funding/record` 

**参数**

| 名称 | 位置 | 描述| 是否必需| 类型 |
| ---- | ---------- | ----------- | -------- | ---- |
| X-Company-Key | header | company key | Yes | string |
| id | query | record id | Yes | string |
**响应结果**

值 | 类型 | 描述
--------- | ------- | ---------
id| string |  record id
fromType| string |  from type: enum[EXCHANGE,WALLET]
fromID| string |  from id
fromName| string |  from name
toType| string |  to type: enum[EXCHANGE,WALLET]
toID| string |  to id
toName| string |  to name
status| string |  record status
assetName| string |  coin name
amount| string |  amount
fee| string |  fee amount
remark| string |  record remark
createdAt| number |  unix timestamp, seconds

# 业务 API
业务 API 仅对业务合作方开放。API 权限通过 [ECC signature](#ecc) 认证，合作方生成密钥，然后将公钥配置到系统里方可使用；用户数据权限通过用户授权合作方后获得的 token 验证。

## 公共参数
对于 GET 请求：

| 名称 | 位置 | 描述| 是否必需| 类型 |
| ---- | ---------- | ----------- | -------- | ---- |
| X-BUSINESS-KEY | header | 业务合作方 key，用于标识业务合作方 | Yes | string |
| sigR | query | ECC signature 参数，生成方法见[ECC signature](#ecc) | Yes | string |
| sigS | query | ECC signature 参数，生成方法见[ECC signature](#ecc) | Yes | string |
| timestamp | query | timestamp，ECC signature 参数，生成方法见[ECC signature](#ecc) | Yes | number |

对于 POST/PUT 请求：

| 名称 | 位置 | 描述| 是否必需| 类型 |
| ---- | ---------- | ----------- | -------- | ---- |
| X-BUSINESS-KEY | header | 业务合作方 key，用于标识业务合作方 | Yes | string |
| sig | body | ECC signature 参数，生成方法见[ECC signature](#ecc) | Yes | object |
| timestamp | body | timestamp，结合sig验证 | Yes | number |

sig:

值 | 类型 |
--------- | ------- |
r | string |
s | string |


## 用户
### 更新 token
**描述:** 用户授权合作方后跳转至合作方页面，合作方会从 url 获得 token ，用来在其他接口中标识用户，出于安全原因 token 仅在短时间内有效（不超过2小时），过期后需要通过此接口更新；也可在需要旧 token 失效时调用此接口，获取到新 token 后旧 token 失效，不可再调用此接口；注意旧的 token 如果已过期调此接口时还需要传入用户的 Google 二次验证码 otpCode ，若 token 遗失需要用户重新授权

#### HTTP请求 
`PUT /api/v1/business/client/token`

**参数**

| 名称 | 位置 | 描述| 是否必需| 类型 |
| ---- | ---------- | ----------- | -------- | ---- |
| token | body | 旧的 token | Yes | string |
| ttl | body | 新 token 的有效期（秒） | Yes | number |
| otpCode | body | 用户的 Google Authentication Code | 旧的 token 过期时必需 | string |

**响应结果**

值 | 类型 | 描述
--------- | ------- | ---------
token | string | 新的 token
exipredAt | number | token 过期时间

### 获取用户的基本信息
**描述:** 获取用户的基本信息

#### HTTP请求 
`GET /api/v1/business/client` 

**参数**

| 名称 | 位置 | 描述| 是否必需| 类型 |
| ---- | ---------- | ----------- | -------- | ---- |
| userToken | query | 用于验证用户身份 | Yes | string |

**响应结果**

值 | 类型 | 描述
--------- | ------- | ---------
id | number | the user id
name | string | the user name

## 钱包
### 获取用户钱包余额
**描述:** 获取所有资产余额

#### HTTP请求 
`GET /api/v1/business/wallet/balances` 

**参数**

| 名称 | 位置 | 描述| 是否必需| 类型 |
| ---- | ---------- | ----------- | -------- | ---- |
| userToken | query | 用于验证用户身份 | Yes | string |

**响应结果**

值 | 类型 | 描述
--------- | ------- | ---------
balances | array | the wallet balance list

balance:

值 | 类型 | 描述
--------- | ------- | ---------
assetID | number | the asset id
assetName | string | the asset name
total | string | the asset total balance
available | string | the asset available balance

### 锁定用户钱包金额
**描述:** 锁定特定币种的金额，锁定的部分将从余额的 available 中移除

#### HTTP请求 
`PUT /api/v1/business/balance/lock`

**参数**

| 名称 | 位置 | 描述| 是否必需| 类型 |
| ---- | ---------- | ----------- | -------- | ---- |
| userToken | body | 用于验证用户身份 | Yes | string |
| assetID | body | asset id | Yes | number |
| amount | body | lock amount | Yes | string |

**响应结果**

值 | 类型 | 描述
--------- | ------- | ---------
total | string | the asset total balance
available | string | the asset available balance

### 解锁用户钱包金额
**描述:** 解锁特定币种的金额，解锁的部分将回到余额的 available 中

#### HTTP请求 
`PUT /api/v1/business/balance/lock`

**参数**

| 名称 | 位置 | 描述| 是否必需| 类型 |
| ---- | ---------- | ----------- | -------- | ---- |
| userToken | body | 用于验证用户身份 | Yes | string |
| assetID | body | asset id | Yes | number |
| amount | body | unlock amount | Yes | string |

**响应结果**

值 | 类型 | 描述
--------- | ------- | ---------
total | string | the asset total balance
available | string | the asset available balance

### 交换币种
**描述:** 在用户钱包间交换币种，将一个币种从源钱包余额的 locked 划转到目标钱包余额的 available，同时将另一个币种从目标钱包余额的 locked 划转到源钱包余额的 available，交换成功后会在源钱包和目标钱包都生成一笔转出订单和一笔转入订单

#### HTTP请求 
`POST /api/v1/business/swap`

**参数**

| 名称 | 位置 | 描述| 是否必需| 类型 |
| ---- | ---------- | ----------- | -------- | ---- |
| sequence | body | 此次交换的唯一标识，防止重复请求 | Yes | string |
| from | body | from user token | Yes | string |
| fromAssetID | body | from asset id | Yes | number |
| fromAmount | body | from asset amount | Yes | string |
| to | body | to user token | Yes | string |
| toAssetID | body | to asset id | Yes | number |
| toAmount | body | to asset amount | Yes | string |
| note | body | note | No | string |

**响应结果**

值 | 类型 | 描述
--------- | ------- | ---------
id | number | the swap id

### 获取用户钱包订单列表
**描述:** 获取订单列表

#### HTTP请求 
`GET /api/v1/business/orders` 

**参数**

| 名称 | 位置 | 描述| 是否必需| 类型 |
| ---- | ---------- | ----------- | -------- | ---- |
| userToken | query | 用于验证用户身份 | Yes | string |
| page | query | page, 默认1 | No | number |
| amount | query | item count on this page, 默认10 | No | number |

**响应结果**

值 | 类型 | 描述
--------- | ------- | ---------
orders | array | the wallet order list
totalCount | number | order total count

orders:

值 | 类型 | 描述
--------- | ------- | ---------
id | number | the order id
assetID | number | the asset id
assetName | string | the asset name
type | string | the order type, TRANSFER_IN/TRANSFER_OUT
bizType | string | the order bizType, SWAP
bizID | number | the order business ID, e.g. swap id for the SWAP order
amount | string | the order amount
status | string | status, DONE
createdAt | number | timestamp, create time

# 回调

## 订单
> 订单通知发送的 JSON 结构如下:

```json
{
  "id": "2XB0eKAvj7KvjZDk59zl",
  "withdrawID": "7Cab38EA42538f4D8C2",
  "bizType": "DEPOSIT",
  "coinName": "ETH",
  "type": "ETH",
  "state": "DONE",
  "memo": "",
  "value": "1.000000000000000000",
  "fee": "0.000000000000000000",
  "from": "0xF0706B7Cab38EA42538f4D8C279B6F57ad1d4072",
  "to": "0x29152c850456899A78178622B6543BBFfC224495",
  "txid": "0x8487e23bbf71f1763e015598283ae891cc5ea8d444f87a0a60a0b5eb7e1a4d59",
  "n": 0,
  "block": 13721091,
  "affirmativeConfirmation": 20,
  "confirmations": 27,
  "sign": "796dde931a15c98edc3dfdb65a2c2addfde422f217a1f6934be9226542839aa0"
}
```

**回调结果**

值 | 类型 | 描述
--------- | ------- | ---------
id | string | the order ID
withdrawID | string | withdraw id
coinName | string | unique token name
txid | string | transaction hash
state | string | order state
bizType | string | order type
type | string | token type
from | string | transaction input
to | string | transaction output
value | string | transaction value
affirmativeConfirmation | number | affirmative confirmation of the blockchain
confirmations | number | number of transaction confirmations
fee | string | fee burnt for the transaction
block | number | the block transaction mined in
memo | string | order note, editable on admin
n | number | the order index
sign | string | hex string, sign parameters with HMACSHA256

### 签名
上面的 sign 参数可以通过下面的方法验证，以保证这个回调结果的发送方身份:

1. 形成一个字符串消息（排序），其中包含数据prarams（排除 sign）。

    如果body params是: 
</br>
`
{
  "id": "2XB0eKAvj7KvjZDk59zl",
  "withdrawID": "7Cab38EA42538f4D8C2",
  "bizType": "DEPOSIT",
  "coinName": "ETH",
  "type": "ETH",
  "state": "DONE",
  "memo": "",
  "value": "1.000000000000000000",
  "fee": "0.000000000000000000",
  "from": "0xF0706B7Cab38EA42538f4D8C279B6F57ad1d4072",
  "to": "0x29152c850456899A78178622B6543BBFfC224495",
  "txid": "0x8487e23bbf71f1763e015598283ae891cc5ea8d444f87a0a60a0b5eb7e1a4d59",
  "n": 0,
  "block": 13721091,
  "affirmativeConfirmation": 20,
  "confirmations": 27
}
`

    消息字符串如下:
</br>
`
affirmativeConfirmation=20&bizType=DEPOSIT&block=13721091&coinName=ETH&confirmations=27&fee=0.000000000000000000&from=0xF0706B7Cab38EA42538f4D8C279B6F57ad1d4072&id=2XB0eKAvj7KvjZDk59zl&memo=&n=0&state=DONE&to=0x29152c850456899A78178622B6543BBFfC224495&txid=0x8487e23bbf71f1763e015598283ae891cc5ea8d444f87a0a60a0b5eb7e1a4d59&type=ETH&value=1.000000000000000000&withdrawID=7Cab38EA42538f4D8C2
`

2. 使用HMAC-SHA256对消息进行签名。如果 AppSecret 是 `exzYZT8IubM9Jxq1PWU5QjZ0JFP81bvCmlf1fFjW0b87Zr6eAMdofeYlhAMZxPzo`, 那么sign param 如下:
</br>
`
fb0f53f33bba4cfa4bcb2c81e976bbe817633ba87a9904b6c3de293da3805cb3
`

# 签名
1. G获取当前的时间戳（秒）和nonce（随机字符串）。请确保时间戳错误不超过5分钟，nonce不在10分钟内重复。

2. 形成一个字符串消息（排序），其中包含数据prarams，上述时间戳和随机字符串。

    如果body params是: 
</br>
`
{
  "mode": "auto"
}
`

    消息字符串如下:
</br>
`
mode=auto&nonce=15833762841261615239762485&timestamp=1583376284
`

3. 使用HMAC-SHA256对消息进行签名。如果 AppSecret 是 `yeTJ3EnOkyQQEjhTMVqn165Dqjp43bhTwXLIv25Ycdu8qwDOyqpa0WV54C6sO4HW`, 那么sign param 如下:
</br>
`
7042a9fd6deea017be7ad76dfb48e4c36feca279819630c870a628f5352c9044
`

4. 发送请求的格式与 [一般结构](#general-structure)中所述的相同。

# AES 加密
加密有两部分: key 和 iv。 key 是 sha256(CompanySecret)， iv 是一个长度为16的随机字节。以下为用python编写的加密appSecret的例子。

```python
import base64
import hashlib
from Crypto.Cipher import AES

def _pad(s):
    return s + (AES.block_size - len(s) % AES.block_size) * chr(AES.block_size - len(s) % AES.block_size)

def _unpad(s):
    return s[:-ord(s[len(s)-1:])]

companySecret = b'kfTKfuSV5oiuFZENI6tv1OwfiJ1tEv70HnmJsVxNGCu2YEKqEq2QHpBGgqwRa29R'
appSecret = 'nn2oh58iuc1sg2adya1golz35cowjbz7r0hfjt2ey6b4fc4xyq6kdopp9tlp0ko3'

aeskey = bytes.fromhex(hashlib.sha256(companySecret).hexdigest())
iv = base64.b64decode('MTIzNDU2Nzg5MDEyMzQ1Ng==')

decipher = AES.new(aeskey, AES.MODE_CBC, IV=iv)
encryptedAppSecret = decipher.encrypt(_pad(appSecret))
base64EncryptedAppSecret = base64.b64encode(encryptedAppSecret)
print(base64EncryptedAppSecret)

decipher = AES.new(aeskey, AES.MODE_CBC, IV=iv)
plaintext = _unpad(decipher.decrypt(base64.b64decode(base64EncryptedAppSecret)))
print(plaintext)
```

# ECC 签名
>  Javascript code of building the message to be signed:

```js
function _buildMsg (obj, opts = {}) {
  let sortRule = opts.sort || 'key-alphabet'
  let arr = []
  if (_.isArray(obj)) {
    arr = obj.map((o, i) => ({
      k: (sortRule === 'kvpair' || sortRule === 'value') ? '' : i,
      v: _buildMsg(o, opts)
    }))
  } else if (_.isObject(obj)) {
    for (let k in obj) {
      if (obj[k] !== undefined) {
        arr.push({ k, v: _buildMsg(obj[k], opts) })
      }
    }
  } else if (obj === undefined || obj === null) {
    return ''
  } else {
    return obj.toString()
  }
  // Sort Array
  arr.sort((a, b) => {
    let aVal
    let bVal
    switch (sortRule) {
      case 'key':
        aVal = a.k
        bVal = b.k
        break
      case 'key-alphabet':
        aVal = a.k.toString()
        bVal = b.k.toString()
        break
      case 'value':
        aVal = a.v
        bVal = b.v
        break
      case 'kvpair':
      default:
        aVal = a.k.toString() + a.v
        bVal = b.k.toString() + b.v
        break
    }
    if (aVal < bVal) {
      return -1
    } else if (aVal === bVal) {
      return 0
    } else {
      return 1
    }
  })
  // Build message
  return arr.reduce((lastMsg, curr) => {
    return lastMsg + curr.k + curr.v
  }, '')
}
```

*构建 GET 请求的签名步骤::*

1. 获取当前的时间戳
2. 将时间戳和其他所有请求参数按字母排序生成一个字符串，字符串像下面这样:
</br>
```
timestamp1557913602438
```
3. 使用 sha3 keccak256 编码上面的字符串
4. 使用私钥通过 ecdsa 签名上面编码好的字符串
5. 将签名结果的 r 和 s 进行 base64 编码后放入请求参数 sigR 和 sigS 中，时间戳放入 timestamp 中，假设私钥(hex编码)为 1cc64d8767ff6fe618a7794d1e16cced03ab35715d58d48029de87b475abef85 ，那么最终的 sigR 和 sigS 为
</br>
```
sigR = '2vS/6a+4+X0wyDyqAw5gTcLS02cCvH+CsSDIOsStPvc='
sigS = 'S7TIyw5oYkoBUC0mBUIHxabMU4slREcMAMYo74ZO2wM='
```

*构建 POST/PUT 请求的签名步骤:*

1. 获取当前的时间戳
2. 将body中的所有请求参数和时间戳按字母排序生成一个字符串（右侧是js的示例代码），以交换币种接口为例（{sequence: 'abcd', from: 'walletwovldjmmmqj2m0n9', to: 'walletp2q8yjerrr6owxmr', fromAssetID: 1, fromAmount: '123', toAssetID: 2, toAmount: '321', note: 'test sig', timestamp: 1557913602438}），字符串像下面这样:
</br>
```
fromwalletwovldjmmmqj2m0n9fromAmount123fromAssetID1notetest sigsequenceabcdtimestamp1557913602438towalletp2q8yjerrr6owxmrtoAmount321toAssetID2
```
3. 使用 sha3 keccak256 编码上面的字符串
4. 使用私钥通过 ecdsa 签名上面编码好的字符串
5. 将签名结果的 r 和 s 进行 base64 编码后放入请求参数 sig 中，时间戳放入 timestamp 中，假设私钥(hex编码)为 1cc64d8767ff6fe618a7794d1e16cced03ab35715d58d48029de87b475abef85 ，那么最终的 sig 为
</br>
```
sig: {
  r: 'Il4vOx0mIfQLRwFQt9E4480IsuwyCvPOHvTxWnv+CM0=',
  s: 'Psdc5NI36LE+R/X6qVvumtjTvo0ufqBBqEBB62FRTh0='
}
```
