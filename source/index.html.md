--- 

title: HashKey Custody API Documentation

language_tabs: 
   - shell
   - javascript
   - go
   - java

toc_footers: 
   - <a href='https://github.com/HKDAG/hashkey-custody-api-docs'>Documentation</a>

includes:
  - errors

search: true 

---

# Introduction 

Hashkey Custody provides a simple and robust RESTful API and client SDK to integrate digital currency wallets with your application. Feel free to check out our [NodeJs SDK](https://github.com/HKDAG/hashkey-custody-sdk-nodejs.git), [Go SDK](https://github.com/nbltrust/hashkey-custody-sdk-go), [Java SDK](https://github.com/HKDAG/jadepool-saas-sdk-java.git).We have 2 level API, management API and wallet API.

The management API enables the following:

- Creation of Wallets

The wallet API enables the following:

- Wallet balance and transaction listing
- Transaction creation
- Transaction monitoring and notifications

# Environments

HashKey Custody has two separate environments available for development and production. For security reasons, all API requests are made using TLS over HTTPS.

## Production Environment
The production endpoint is live and used by partners.

## Test Environment
The test environment is entirely separate from the production environment and there is no overlap in either data or accounts.

This environment is connected to the TestNet networks of various digital currencies we support. Tokens on these networks can be obtained from faucets and do not represent real money.

# Coin / Digital Currency Support
For production environment, please refer the document.
> https://XXXXX

For test Environment, please refer the list:

| Coin | Blockchain | Description |
| ---- | ---------- | ----------- |
| ETH | Ethereum | Sepolia Testnet |
| USDT | Ethereum | Sepolia Testnet, ERC20 |
| TRX | Tron | Tron Testnet |
| BTC | Bitcoin | public Testnet |

# API Authentication



# General Structure

This is the general request structure for verifing the request by server.

**Parameters**

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| timestamp | query/body | unix timestamp, seconds | Yes | string |
| nonce | query/body | nonce, random string, not repeated in 10 minutes | Yes | string |
| sign | query/body | hex string, sign parameters with HMACSHA256 | Yes | string |

The parameters `timestamp`, `nonce` and `sign` are located in query for GET requests, but located in the body for POST requests.

if POST request, the body looks like this:

`
{ 
  "timestamp": 1583376284,
  "nonce": "15833762841261615239762485",
  "mode": "auto",
  "sign":"7042a9fd6deea017be7ad76dfb48e4c36feca279819630c870a628f5352c9044"
}
`

if GET request, the url looks like this:

`
/api/v1/app/balance/ETH?timestamp=1583374390&nonce=1583374390314165815853245&sign=7c482cf15d9d25772eee2c43bd146c8136472a055b4587b9fb22d02720037875
`

<font size=4>The [signature method](#signature) is similar to OAuth.</font>

# Wallet API

```shell
$ git clone https://github.com/HKDAG/hashkey-custody-sdk-go && cd hashkey-custody-sdk-golang
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

The wallet key and secret can be generated in the wallet settings.

For security purpose, it is strongly recommended that you bind an IP address or IP segment to the API Key.

**Warning:** API Key/Secret is closely related to the security of your account and should not be disclosed to anyone at any time. API Key/Secret disclosure may result in the loss of your assets (even if you do not enable access to withdrawals). Please delete the API Key/Secret as soon as you discover it has been compromised.

## Address

### get the latest address 

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

**Summary:** get the latest address, create if not exist

#### HTTP Request 
`GET /api/v1/address/{coinName}` 

**Parameters**

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| X-App-Key | header | app key | Yes | string |
| coinName | path | coinName | Yes | string |

**Response Result**

Value | Type | Description
--------- | ------- | ---------
address | string | the new address
mode | string | address mode

### create a new address 

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

**Summary:** create a new address

#### HTTP Request 
`POST /api/v1/address/{coinName}/new` 

**Parameters**

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| X-App-Key | header | app key | Yes | string |
| coinName | path | coinName | Yes | string |
| mode | body | mode | No | string |

**Response Result**

Value | Type | Description
--------- | ------- | ---------
address | string | the new address
mode | string | address mode

### verify a address 

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

**Summary:** verify a address

#### HTTP Request 
`POST /api/v1/address/{coinName}/verify` 

**Parameters**

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| X-App-Key | header | app key | Yes | string |
| coinName | path | coinName | Yes | string |
| address | body | address | Yes | string |

**Response Result**

Value | Type | Description
--------- | ------- | ---------
address | string | the address to validate
valid | boolean | the address is valid or not

### anti-money laundering check

```shell
$ go run cmd/ctl/main.go "appkey" "appsecret" "CheckAddress" "ETH" "0x9bf65CDF5729b9588F6bAEBb2Aa2926472D4a035"
code: 0
message: success
data:
{
  "hitRule": false
}
```

```go
	result, _ = app.CheckAddress("ETH", "0x9bf65CDF5729b9588F6bAEBb2Aa2926472D4a035")
```

**Summary:** anti-money laundering check

#### HTTP Request 
`POST /api/v1/address/{coinName}/check`

**Parameters**

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| X-App-Key | header | app key | Yes | string |
| coinName | path | coinName | Yes | string |
| address | body | address | Yes | string |

**Response Result**

Value | Type | Description
--------- | ------- | ---------
hitRule | boolean | the address hit the anti-money rule or not

## Wallet
### get all available assets 

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

**Summary:** get all available assets

#### HTTP Request 
`GET /api/v1/app/allAssets` 

**Parameters**

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| X-App-Key | header | app key | Yes | string |

**Response Result**

Value | Type | Description
--------- | ------- | ---------
assets | array | the wallet coin list

### get wallet balances

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
      "price": "9816.344189",
      "outLocked": "11.2",
      "inLocked": "11.2"
    },
    {
      "balance": "1.0",
      "money": "246.565827",
      "name": "ETH",
      "price": "246.565827",
      "outLocked": "11.2",
      "inLocked": "11.2"
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

**Summary:** get all asset balances, include the amount by USD and the price by USD

#### HTTP Request 
`GET /api/v1/app/balances` 

**Parameters**

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| X-App-Key | header | app key | Yes | string |

**Response Result**

Value | Type | Description
--------- | ------- | ---------
total | string | total money(USD)
balances | array | the wallet balance list

balance:

Value | Type | Description
--------- | ------- | ---------
balance | string | the coin balance
money | string | the amount(USD) equal to the balance
name | string | the coin name
price | string | the coin price(USD)
outLocked | string | the coin transfer out amount under locked
inLocked | string | the coin transfer in amount under locked

### add wallet asset

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

**Summary:** add wallet asset

#### HTTP Request 
`POST /api/v1/app/assets` 

**Parameters**

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| X-App-Key | header | app key | Yes | string |
| coinName | body | asset name | Yes | array |

**Response Result**

Value | Type | Description
--------- | ------- | ---------

### get wallet single asset balance 

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

**Summary:** get wallet single asset balance

#### HTTP Request 
`GET /api/v1/app/balance/{coinName}` 

**Parameters**

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| X-App-Key | header | app key | Yes | string |
| coinName | path | coin type | Yes | string |

**Response Result**

Value | Type | Description
--------- | ------- | ---------
balance | string | the coin balance
inLocked | string | the coin transfer in amount under locked
inLockedFee | string | the coin transfer in fee under locked
outLocked | string | the coin transfer out amount under locked
outLockedFee | string | the coin transfer out fee under locked
delegateAmount | string | the coin delegate amount
delegateInLocked | string | the coin delegate transfer in amount under locked
delegateOutLocked | string | the coin delegate transfer out amount under locked
undelegateAmount | string | the undelegate coin amount
undelegateInLocked | string | the undelegate coin transfer in amount under locked
undelegateOutLocked | string | the undelegate coin transfer out amount under locked

### get wallet assets 

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

**Summary:** get wallet assets

#### HTTP Request 
`GET /api/v1/app/assets` 

**Parameters**

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| X-App-Key | header | app key | Yes | string |

**Response Result**

Value | Type | Description
--------- | ------- | ---------
assets | array | the wallet coin list

### get wallet assets detail

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
      },
      "withdrawMinAmount": "0.0001"
    }
  ]
}
```

**Summary:** get wallet assets detail

#### HTTP Request 
`GET /api/v1/app/assetsWithID`

**Parameters**

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| X-App-Key | header | app key | Yes | string |

**Response Result**

Value | Type | Description
--------- | ------- | ---------
assets | array | the wallet coin list

asset:

Value | Type | Description
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
withdrawMinAmount | string | min withdraw amount

priority:

Value | Type | Description
--------- | ------- | ---------
fee | string | fee with the priority
priority | string | the priority, set into the withdraw request

### get wallet attributes 

```shell
$ go run cmd/ctl/main.go "appkey" "appsecret" "GetAppInfo"
code: 0
message: success
data:
{
  "bizType": "NORMAL",
  "description": "string",
  "id": "L6RayqPn4jXExW0",
  "name": "test",
  "status": "NORMAL"
}
```

```go
	result, _ = app.GetAppInfo()
```

**Summary:** get wallet attributes

#### HTTP Request 
`GET /api/v1/app/info` 

**Parameters**

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| X-App-Key | header | app key | Yes | string |

**Response Result**

Value | Type | Description
--------- | ------- | ---------
id | string | the wallet id
name | string | the wallet name
description | string | the wallet description
status | string | the wallet status, NORMAL/ABNORMAL
bizType | string | the wallet type, NORMAL

### get wallet orders

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

**Summary:** get wallet orders

#### HTTP Request 
`GET /api/v1/app/orders` 

**Parameters**

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| X-App-Key | header | app key | Yes | string |
| page | query | page, e.g. 1 | Yes | string |
| amount | query | item count on this page, e.g. 10 | Yes | string |
| coins | query | coin list, can be empty string | No | string |
| state | query | order state, can be empty string | No | string |
| bizType | query | order type, TRANSFER_IN/TRANSFER_OUT/WITHDRAW/DEPOSIT | No | string |

**Response Result**

Value | Type | Description
--------- | ------- | ---------
orders | array | order list
totalAmount | number | the total count of orders

order:

Value | Type | Description
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
createdAt | number |  unix timestamp, seconds
finalizedAt | number |  unix timestamp, seconds
relatedOrderId | string | related order id


### get wallet single order

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
  "message": "",
  "createdAt": 1569306519,
  "finalizedAt": 1569306519,
  "relatedOrderId": "orderrNXBQGJlw09apVyg4nDo"
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

**Summary:** get wallet single order

#### HTTP Request 
`GET /api/v1/app/order/{id}` 

**Parameters**

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| X-App-Key | header | app key | Yes | string |
| id | path | order id | Yes | string |

**Response Result**

Value | Type | Description
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
createdAt | number |  unix timestamp, seconds
finalizedAt | number |  unix timestamp, seconds
relatedOrderId | string | related order id
### update wallet order

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

**Summary:** update wallet order

#### HTTP Request 
`PUT /api/v1/app/order/{id}` 

**Parameters**

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| X-App-Key | header | app key | Yes | string |
| id | path | order id | Yes | string |
| note | body | note | No | string |

**Response Result**

Value | Type | Description
--------- | ------- | ---------

### withdraw 

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

**Summary:** withdraw

#### HTTP Request 
`POST /api/v1/app/{coinName}/withdraw` 

**Parameters**

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| X-App-Key | header | app key | Yes | string |
| coinName | path | coin type | Yes | string |
| id | body | withdraw id | Yes | string |
| to | body | receive address | Yes | string |
| value | body | withdraw amount | Yes | string |
| memo | body | address memo | No | string |
| note | body | note | No | string |
| priority | body | priority | No | string |

**Response Result**

Value | Type | Description
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

### transfer

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

**Summary:** transfer

#### HTTP Request 
`POST /api/v1/app/{coinName}/transfer` 

**Parameters**

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| X-App-Key | header | app key | Yes | string |
| coinName | path | coin type | Yes | string |
| to | body | receive wallet id | Yes | string |
| value | body | transfer amount | Yes | string |
| note | body | transfer note, can be empty string | Yes | string |
| message | body | transfer message, can be empty string | Yes | string |

**Response Result**

Value | Type | Description
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

## White list

### add white address

```go
	result, _ = app.AddWhiteAddress("ETH", "address")
```

**add withdraw/deposite white address**

#### HTTP Request 
`POST /api/v1/address/whitelist/add` 

**Parameters**

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| X-App-Key | header | app key | Yes | string |
| coinName | body | coin type | Yes | string |
| address | body | receive wallet address | Yes | string |

**Response Result**

Value | Type | Description
--------- | ------- | ---------
code | int | the result status <br /> 0: success <br />10006：internal error<br /> 10005: bad params <br />20003: duplicate request
message | string | transfer message

## Market

### current price

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

**Summary:** get the asset price by USD

#### HTTP Request 
`GET /api/v1/market/{coinName}`

**Parameters**

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| X-App-Key | header | app key | Yes | string |
| coinName  | path | asset name | Yes | string |

**Response Result**

Value | Type | Description
--------- | ------- | ---------
price | string | current price

## System

### current timestamp

```shell
$ go run cmd/ctl/main.go "appkey" "appsecret" "SystemGetTime"
code: 0
message: success
data:
{
  "timestamp": 1590391287
}
```

**Summary:** get system current timestamp

#### HTTP Request 
`GET /api/v1/system/time`

**Parameters**

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| X-App-Key | header | app key | Yes | string |

**Response Result**

Value | Type | Description
--------- | ------- | ---------
timestamp | number | current timestamp

# Management API

```shell
$ git clone https://github.com/HKDAG/hashkey-custody-sdk-go && cd hashkey-custody-sdk-golang
```

```javascript
const teamKey = 'TyTLvCnHINbWZQag88hhmMz1'
const teamSecret = 'uf0rPlTluGnIllGqx0X1os4hQ6rOdXDxStiN4qGd79lS6yeHZaOK4ldvRv1TBqr6'
const apiAddr = 'http://127.0.0.1:8092'

const api = new API(teamKey, teamSecret, apiAddr)
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

The management key and secret can be generated in the team settings.

For security purpose, it is strongly recommended that you bind an IP address or IP segment to the API Key.

**Warning:** API Key/Secret is closely related to the security of your account and should not be disclosed to anyone at any time. Please delete the API Key/Secret as soon as you discover it has been compromised.

## Wallet
### create

```shell
$ go run cmd/ctl/main.go "teamkey" "teamsecret" "CreateWallet" "name" "password" "https://noti.domain/callback"
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

**Summary:** create a general wallet

#### HTTP Request 
`POST /api/v1/app` 

**Parameters**

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| X-Company-Key | header | team api key | Yes | string |
| name | body | the wallet name | Yes | string |
| password  | body | the wallet password, should encrypted by [AES encryption](#aes-encryption) and base64 encoded | Yes | string |
| description | body | the wallet description | Yes | string |
| webHook | body | the url will be called when send [a notification](#callback) | Yes | string |
| aesIV | body | base64 encoded [AES encryption](#aes-encryption) iv, must be 16 bytes, used by encrypting app password and secret | Yes | string |

**Response Result**

Value | Type | Description
--------- | ------- | ---------
id | string | wallet id
appKey | string | the appkey placed in the header of [Wallet API](#wallet-api)
encryptedAppSecret | string | the base64 encoded [AES encrypted](#aes-encryption) appsecret, the appsecret used for the [signature](#signature) of [Wallet API](#wallet-api)

### get appkeys

```shell
$ go run cmd/ctl/main.go "teamkey" "teamsecret" "GetWalletKeys" "appID"
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

**Summary:** get appkeys(include appsecret)

#### HTTP Request 
`GET /api/v1/app/{appID}/keys` 

**Parameters**

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| X-Company-Key | header | team api key | Yes | string |
| appID | path | wallet id | Yes | string |
| aesIV | query | base64 encoded [AES encryption](#aes-encryption) iv, must be 16 bytes, used by encrypting app secret in the response | Yes | string |

**Response Result**

Value | Type | Description
--------- | ------- | ---------
keys | array | the appkey list

key:

Value | Type | Description
--------- | ------- | ---------
appKey | string | the appkey placed in the header of [Wallet API](#wallet-api)
enable | boolean | enable flag, the appkey will been forbidden if set false
encryptedAppSecret | string | the base64 encoded [AES encrypted](#aes-encryption) appsecret, the appsecret used for the [signature](#signature) of [Wallet API](#wallet-api)

### update appkey

```shell
$ go run cmd/ctl/main.go "teamkey" "teamsecret" UpdateWalletKey "appKey" true
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

**Summary:** update appkey attributes

#### HTTP Request 
`PUT /api/v1/appKey/{appKey}` 

**Parameters**

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| X-Company-Key | header | team api key | Yes | string |
| appKey | path | wallet appkey | Yes | string |
| enable | body | enable flag, the appkey will been forbidden if set false | Yes | boolean |

**Response Result**

Value | Type | Description
--------- | ------- | ---------

## Finance
### Income and Expenditure

```shell
$ go run cmd/ctl/main.go "companykey" "companysecret" "GetFinanceOrders" "1" "10" "https://stg-xpert.hashkeydev.com/saas-api"
code: 0
message: success
data:
{
  "orders": [
    {
      "amount": "0.100000000000000000",
      "fee": "0.000000000000000000",
      "balance": "2.000000000000000000",
      "bizOrderID": "ordersxv3z41mwyglorr0gnl2keop5",
      "bizType": "DEPOSIT",
      "coinName": "USDT",
      "createdAt": 1737014596,
      "exchangeID": "",
      "exchangeName": "",
      "from": "0x53f1F06D87683c7443B316DFD994ff1562313888",
      "fromType": "ADDRESS",
      "id": "finorderkewm30o8568p8l9j79qp12zn",
      "note": "",
      "operator": "",
      "opsType": "ADMIN",
      "subType": "DEPOSIT",
      "to": "0xf128c288b92009CED44D654cDfc111Dc5248cBEE",
      "toType": "ADDRESS",
      "txid": "0x3610d05727fa0ff997653e54c69df8c3065b15dd9744130b9887151d58f29507",
      "updatedAt": 1737014709,
      "walletID": "walletylr07eg5r4j2mkpw",
      "walletName": "wallet2"
    }
  ],
  "total": 1
}
```

```go
	result, _ = company.GetFinanceOrders(map[string]interface{}{
		"page":1,
    "pageSize":10
	})
```

#### HTTP Request 
`GET /api/v1/finance/orders` 

> Download Excel file: `GET /api/v1/finance/orders/download`

**Params**

| Name | Located in | Description| Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| X-Company-Key | header | company key | Yes | string |
|wallets|query|wallet id list, multiple values separated by commas|No|string|
|bizTypes|query|Type:<br>DEPOSIT<br>WITHDRAW<br>BATCH_WITHDRAW<br>BATCH_TRANSFER<br>WALLET_TRANSFER|No|string|
|subType|query|Subtype:<br>IN<br>OUT|No|string|
|coins|query|token list (multiple values separated by commas)|No|string|
|start|query|Start timestamp (seconds)|Yes|number|
|end|query|End timestamp (seconds)|Yes|number|
|page|query|Page (starting value is 1)|No|number|
|pageSize|query|Page size|No|number|

**Response Result**

```json
{
  "code": 0,
  "data": {
    "orders": [
      {
        "amount": "0.106000000000000000",
        "fee": "0.000000000000000000",
        "balance": "10.430999300000000000",
        "bizOrderID": "ordersmq41ndkz1232323232r",
        "bizType": "WITHDRAW",
        "coinName": "BTC",
        "createdAt": 1733971691,
        "exchangeID": "",
        "exchangeName": "",
        "from": "0x510F4a8CC103E0F17915A7630F1524Bb9AF01aB6",
        "fromType": "ADDRESS",
        "id": "finorder40ymrp87zgywpw4jlqd95e2w",
        "note": "34343",
        "operator": "0x4511@gmail.com",
        "opsType": "ADMIN",
        "subType": "WITHDRAW",
        "to": "0x035221cF1bDcCd8100531B6B6132323232323",
        "toType": "ADDRESS",
        "fee": "0.000000000000000000",
        "txid": "0x01921175f4986bf3bafd2a16a61b886cf12323232323232323",
        "updatedAt": 1733971965,
        "walletID": "walletw5l729323232323",
        "walletName": "BTC_WALLET"
      }
    ],
    "total": 1
  },
  "message": "success"
}
```

Status Code **200**

|Name|Type|Required|Constraints|Description|
|---|---|---|---|---|
|» code|integer|true|none|none|
|» message|string|true|none|none|
|» data|object|true|none|none|
|»» orders|[object]|true|none|Order list|
|»»» amount|string|true|none|Amount|
|»»» fee|string|true|none|Fee|
|»»» balance|string|true|none|Balance|
|»»» bizOrderID|string|true|none|Business order id|
|»»» bizType|string|true|none|Type:<br>DEPOSIT-Deposit<br>WITHDRAW-Withdraw<br>BATCH_WITHDRAW-Batch withdraw<br>BATCH_TRANSFER-Batch transfer<br>WALLET_TRANSFER-Wallet transfer|
|»»» coinName|string|true|none|Coin name|
|»»» createdAt|integer|true|none|Created at|
|»»» exchangeID|string|true|none|Exchange id|
|»»» exchangeName|string|true|none|Exchange name|
|»»» from|string|true|none|From|
|»»» fromType|string|true|none|From type|
|»»» id|string|true|none|Order id|
|»»» note|string|true|none|Note|
|»»» operator|string|true|none|Operator|
|»»» opsType|string|true|none|Ops type: ADMIN-Admin operation, API-System operation|
|»»» subType|string|true|none|Subtype:<br>WITHDRAW-Withdraw<br>DEPOSIT-Deposit<br>TRANSFER_OUT-Transfer out<br>TRANSFER_IN-Transfer in|
|»»» to|string|true|none|Target address or wallet name|
|»»» toType|string|true|none|Target type: ADDRESS-Address, WALLET-Wallet|
|»»» txid|string|true|none|Transaction hash|
|»»» updatedAt|integer|true|none|Updated at|
|»»» walletID|string|true|none|Wallet id|
|»»» walletName|string|true|none|Wallet name|
|»» total|integer|true|none|Total|


### Financial Statement


```shell
$ go run cmd/ctl/main.go "companykey" "companysecret" "GetFinanceReport" "1" "10" "https://stg-xpert.hashkeydev.com/saas-api"
code: 0
message: success
data:
{
  "reports": [
      {
          "coin": "USDT-ERC20",
          "deposit": "0.000000000000000000",
          "depositCount": 0,
          "endingBalance": "0.000000000000000000",
          "loanPurchase": "0.000000000000000000",
          "loanPurchaseCount": 0,
          "loanSettle": "0.000000000000000000",
          "loanSettleCount": 0,
          "openingBalance": "0.000000000000000000",
          "price": 1.0002579083,
          "priceDeposit": 0,
          "priceEndingBalance": 0,
          "priceLoanPurchase": 0,
          "priceLoanSettle": 0,
          "priceOpeningBalance": 0,
          "priceTransferIn": 0,
          "priceWithdraw": 0,
          "time": "2024/10/18",
          "transferIn": "0.000000000000000000",
          "wallet": {
              "id": "walletw5l729jn4mgy0843",
              "name": "HBLHSK"
          },
          "withdraw": "0.000000000000000000",
          "withdrawCount": 0
      }
  ],
  "total": 1
}
```

```go
	result, _ = company.GetFinanceReport(map[string]interface{}{
		"page":1,
    "pageSize":10
	})
```

#### HTTP请求 
`GET /api/v1/finance/reports` 

> Download Excel file: `GET /api/v1/finance/reports/download`

**Params**

| Name | Located in | Description| Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| X-Company-Key | header | company key | Yes | string |
|wallets|query|wallet id list, multiple values separated by commas|No|string|
|type|query|DAILY-Daily<br>MONTHLY-Monthly<br>ANNUAL-Annual| 否 |string|
|coins|query|token list (multiple values separated by commas)| 否 |string|
|startDate|query|Start date (yyyy-MM-dd)|Yes|string|
|endDate|query|End date (yyyy-MM-dd)| Yes |string|
|page|query|Page (starting value is 1)|No|number|
|pageSize|query|Page size|No|number|

**Response Result**

```json
{
    "code": 0,
    "data": {
        "reports": [
            {
                "coin": "ETH",
                "deposit": "167.000000000000000000",
                "depositCount": 5,
                "endingBalance": "10.430999300000000000",
                "loanPurchase": "0.000000000000000000",
                "loanPurchaseCount": 0,
                "loanSettle": "0.000000000000000000",
                "loanSettleCount": 0,
                "openingBalance": "0.000000000000000000",
                "priceDeposit": 297166.4704650682,
                "priceEndingBalance": 18561.336798829732,
                "priceLoanPurchase": 0,
                "priceLoanSettle": 0,
                "priceOpeningBalance": 0,
                "priceTransferIn": 0,
                "priceWithdraw": 278605.1336662383,
                "time": "2024",
                "transferIn": "0.000000000000000000",
                "wallet": {
                    "id": "walletw5l729jn4mgy0843",
                    "name": "HBLHSK"
                },
                "withdraw": "156.569000700000000000",
                "withdrawCount": 37
            }
        ],
        "total": 20
    },
    "message": "success"
}
```

Status Code **200**

|Name|Type|Required|Constraints|Description|
|---|---|---|---|---|
|» code|integer|true|none|none|
|» message|string|true|none|none|
|» data|object|true|none|none|
|»» reports|[object]|true|none|Report list|
|»»» coin|string|true|none|Coin|
|»»» deposit|string|true|none|Deposit amount|
|»»» depositCount|integer|true|none|Deposit count|
|»»» endingBalance|string|true|none|Ending balance|
|»»» loanPurchase|string|true|none|Loan purchase amount|
|»»» loanPurchaseCount|integer|true|none|Loan purchase count|
|»»» loanSettle|string|true|none|Loan settle amount|
|»»» loanSettleCount|integer|true|none|Loan settle count|
|»»» openingBalance|string|true|none|Opening balance|
|»»» priceDeposit|number|true|none|Deposit price|
|»»» priceEndingBalance|number|true|none|Ending balance price|
|»»» priceLoanPurchase|number|true|none|Loan purchase price|
|»»» priceLoanSettle|number|true|none|Loan settle price|
|»»» priceOpeningBalance|number|true|none|Opening balance price|
|»»» priceTransferIn|number|true|none|Transfer in price|
|»»» priceWithdraw|number|true|none|Withdraw price|
|»»» time|string|true|none|Time|
|»»» transferIn|string|true|none|Transfer in amount|
|»»» wallet|object|true|none|Wallet|
|»»»» id|string|true|none|Wallet id|
|»»»» name|string|true|none|Wallet name|
|»»» withdraw|string|true|none|Withdraw amount|
|»»» withdrawCount|integer|true|none|Withdraw count|
|»» total|integer|true|none|Total|

# Callback

## Order
> Order notification sends JSON structured like this:

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
  "sign": "fb0f53f33bba4cfa4bcb2c81e976bbe817633ba87a9904b6c3de293da3805cb3",
  "relatedOrderID": "orderrNXBQGJlw09apVyg4nDo"
}
```

**Callbck Result**

Value | Type | Description
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
relatedOrderId | string | related order id

### Signature
In order to prove the message sender's identity, it should be verified by the following steps:

1. Form a String message (sorted) that contains data prarams(exclude the sign param). 

    if body params is: 
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

    The message string looks like this:
</br>
`
affirmativeConfirmation=20&bizType=DEPOSIT&block=13721091&coinName=ETH&confirmations=27&fee=0.000000000000000000&from=0xF0706B7Cab38EA42538f4D8C279B6F57ad1d4072&id=2XB0eKAvj7KvjZDk59zl&memo=&n=0&state=DONE&to=0x29152c850456899A78178622B6543BBFfC224495&txid=0x8487e23bbf71f1763e015598283ae891cc5ea8d444f87a0a60a0b5eb7e1a4d59&type=ETH&value=1.000000000000000000&withdrawID=7Cab38EA42538f4D8C2
`

2. Sign the message using HMAC-SHA256. If the AppSecret is `exzYZT8IubM9Jxq1PWU5QjZ0JFP81bvCmlf1fFjW0b87Zr6eAMdofeYlhAMZxPzo`, the sign param would be like this:
</br>
`
fb0f53f33bba4cfa4bcb2c81e976bbe817633ba87a9904b6c3de293da3805cb3
`

# Signature
1. Get the current timestamp (seconds) and the nonce (random string). Please make sure the timestamp error not exceed 5 minutes and the nonce not repeated in 10 minutes.

2. Form a String message (sorted) that contains data prarams, the timestamp and nonce above. 

    if body params is: 
</br>
`
{
  "mode": "auto"
}
`

    The message string looks like this:
</br>
`
mode=auto&nonce=15833762841261615239762485&timestamp=1583376284
`

3. Sign the message using HMAC-SHA256. If the AppSecret is `yeTJ3EnOkyQQEjhTMVqn165Dqjp43bhTwXLIv25Ycdu8qwDOyqpa0WV54C6sO4HW`, the sign param would be like this:
</br>
`
7042a9fd6deea017be7ad76dfb48e4c36feca279819630c870a628f5352c9044
`

4. Send request as the same format as described in [General Structure](#general-structure).

# AES Encryption
The encryption has two parts: key and iv. The key is sha256(TeamSecret). The iv is a random bytes that length is 16. An example of encrypting appSecret written by python.

```python
import base64
import hashlib
from Crypto.Cipher import AES

def _pad(s):
    return s + (AES.block_size - len(s) % AES.block_size) * chr(AES.block_size - len(s) % AES.block_size)

def _unpad(s):
    return s[:-ord(s[len(s)-1:])]

teamSecret = b'kfTKfuSV5oiuFZENI6tv1OwfiJ1tEv70HnmJsVxNGCu2YEKqEq2QHpBGgqwRa29R'
appSecret = 'nn2oh58iuc1sg2adya1golz35cowjbz7r0hfjt2ey6b4fc4xyq6kdopp9tlp0ko3'

aeskey = bytes.fromhex(hashlib.sha256(teamSecret).hexdigest())
iv = base64.b64decode('MTIzNDU2Nzg5MDEyMzQ1Ng==')

decipher = AES.new(aeskey, AES.MODE_CBC, IV=iv)
encryptedAppSecret = decipher.encrypt(_pad(appSecret))
base64EncryptedAppSecret = base64.b64encode(encryptedAppSecret)
print(base64EncryptedAppSecret)

decipher = AES.new(aeskey, AES.MODE_CBC, IV=iv)
plaintext = _unpad(decipher.decrypt(base64.b64decode(base64EncryptedAppSecret)))
print(plaintext)
```

# Change Log

Release Time | API | New / Update | Description
-------------- | -------------- | -------------- | --------------
2020-09-04 14:00 | - | - | update the documentation