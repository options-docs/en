#  General Info

## General API Information

* The base endpoint is: **https://vapi.binance.com**
* All endpoints return either a JSON object or array.
* Data is returned in ascending order. Oldest first, newest last.
* All time and timestamp related fields are in milliseconds.

### HTTP Return Codes

* HTTP `4XX` return codes are used for malformed requests;
  the issue is on the sender's side.
* HTTP `403` return code is used when the WAF Limit (Web Application Firewall) has been violated.
* HTTP `429` return code is used when breaking a request rate limit.
* HTTP `418` return code is used when an IP has been auto-banned for continuing to send requests after receiving `429` codes.
* HTTP `5XX` return codes are used for internal errors; the issue is on
  Binance's side.
* HTTP `504` return code is used when the API successfully sent the message but not get a response within the timeout period.
  It is important to **NOT** treat this as a failure operation; the execution status is
  **UNKNOWN** and could have been a success.

### Endpoint Error Codes

* Any endpoint can return an ERROR
* Specific error codes and messages defined in [Error Codes](#errorcodes).

### General Information on Endpoints

* For GET endpoints, parameters must be sent as a query string.
* For POST, PUT, and DELETE endpoints, the parameters may be sent as a query string or in the request body with content type application/x-www-form-urlencoded. You may mix parameters between both the query string and request body if you wish to do so.
* Parameters may be sent in any order.
* If a parameter sent in both the query string and request body, the query string parameter will be used.

## testnet environment
* Most of the endpoints can be also used in the testnet platform.
* The REST baseurl for testnet is **https://testnet.binanceops.com**
* The Websocket baseurl for testnet is **wss://testnetws.binanceops.com**

## Endpoint Security Type

* Each endpoint has a security type that determines the how you will interact with it.
* API-keys are passed into the Rest API via the X-MBX-APIKEY header.
* API-keys and secret-keys are case sensitive.
* API-keys can be configured to only access certain types of secure endpoints. For example, one API-key could be used for TRADE only, while another API-key can access everything except for TRADE routes.
* By default, API-keys can access all secure routes.

Security Type | Description 
------------ | ------------ 
NONE | Endpoint can be accessed freely.
TRADE | Endpoint requires sending a valid API-Key and signature.
USER_DATA | Endpoint requires sending a valid API-Key and signature.
USER_STREAM | Endpoint requires sending a valid API-Key.
MARKET_DATA | Endpoint requires sending a valid API-Key.

## SIGNED (TRADE and USER_DATA) Endpoint Security

* SIGNED endpoints require an additional parameter, signature, to be sent in the query string or request body.
* Endpoints use HMAC SHA256 signatures. The HMAC SHA256 signature is a keyed HMAC SHA256 operation. Use your secretKey as the key and totalParams as the value for the HMAC operation.
* The signature is not case sensitive.
* Please make sure the signature is the end part of your query string or request body.
* totalParams is defined as the query string concatenated with the request body.

### Timing security

* A SIGNED endpoint also requires a parameter, timestamp, to be sent which should be the millisecond timestamp of when the request was created and sent.
* An additional parameter, recvWindow, may be sent to specify the number of milliseconds after timestamp the request is valid for. If recvWindow is not sent, it defaults to 5000.
* If the server determines that the timestamp sent by the client is more than one second in the future of the server time, the request will also be rejected.

**Serious trading is about timing. ** Networks can be unstable and unreliable, which can lead to requests taking varying amounts of time to reach the servers. With recvWindow, you can specify that the request must be processed within a certain number of milliseconds or be rejected by the server.
<aside class="notice">
It is recommended to use a small recvWindow of 5000 or less!
</aside>


### SIGNED Endpoint Examples for POST /vapi/v1/order
Here is a step-by-step example of how to send a vaild signed payload from the
Linux command line using `echo`, `openssl`, and `curl`.

Key | Value
------------ | ------------
apiKey | 22BjeOROKiXJ3NxbR3zjh3uoGcaflPu3VMyBXAg8Jj2J1xVSnY0eB4dzacdE9IWn
secretKey | YtP1BudNOWZE1ag5uzCkh4hIC7qSmQOu797r5EJBFGhxBYivjj8HIX0iiiPof5yG


Parameter | Value
------------ | ------------
symbol | BTC-210129-40000-C
side | BUY
type | LIMIT
timeInForce | GTC
quantity | 0.01
price | 2000
recvWindow | 5000
timestamp | 1611825601400


#### Example 1: As a request body

> **Example 1**

> **HMAC SHA256 signature:**

```shell
    $ echo -n "symbol=BTC-210129-40000-C&side=BUY&type=LIMIT&timeInForce=GTC&quantity=0.01&price=2000&recvWindow=5000&timestamp=1611825601400" | openssl dgst -sha256 -hmac "YtP1BudNOWZE1ag5uzCkh4hIC7qSmQOu797r5EJBFGhxBYivjj8HIX0iiiPof5yG"
    (stdin)= 7c12045972f6140e765e0f2b67d28099718df805732676494238f50be830a7d7
```


> **curl command:**

```shell
    (HMAC SHA256)
    $ curl -H "X-MBX-APIKEY: 22BjeOROKiXJ3NxbR3zjh3uoGcaflPu3VMyBXAg8Jj2J1xVSnY0eB4dzacdE9IWn" -X POST 'https://vapi.binance.com/vapi/v1/order' -d 'symbol=BTC-210129-40000-C&side=BUY&type=LIMIT&timeInForce=GTC&quantity=0.01&price=2000&recvWindow=5000&timestamp=1611825601400&signature=7c12045972f6140e765e0f2b67d28099718df805732676494238f50be830a7d7'
    
```

* **requestBody:** 

symbol=BTC-210129-40000-C   
&side=BUY   
&type=LIMIT   
&timeInForce=GTC   
&quantity=0.01  
&price=2000  
&recvWindow=5000   
&timestamp=1611825601400


#### Example 2: As a query string

> **Example 2**

> **HMAC SHA256 signature:**

```shell
    $ echo -n "symbol=BTC-210129-40000-C&side=BUY&type=LIMIT&timeInForce=GTC&quantity=0.01&price=2000&recvWindow=5000&timestamp=1611825601400" | openssl dgst -sha256 -hmac "YtP1BudNOWZE1ag5uzCkh4hIC7qSmQOu797r5EJBFGhxBYivjj8HIX0iiiPof5yG"
    (stdin)= 7c12045972f6140e765e0f2b67d28099718df805732676494238f50be830a7d7
    
```
> **curl command:**

```shell
    (HMAC SHA256)
   $ curl -H "X-MBX-APIKEY: 22BjeOROKiXJ3NxbR3zjh3uoGcaflPu3VMyBXAg8Jj2J1xVSnY0eB4dzacdE9IWn" -X POST 'https://vapi.binance.com/vapi/v1/order?symbol=BTC-210129-40000-C&side=BUY&type=LIMIT&timeInForce=GTC&quantity=0.01&price=2000&recvWindow=5000&timestamp=1611825601400&signature=7c12045972f6140e765e0f2b67d28099718df805732676494238f50be830a7d7'
    
```
* **queryString:**  

symbol=BTC-210129-40000-C   
&side=BUY   
&type=LIMIT   
&timeInForce=GTC   
&quantity=0.01  
&price=2000  
&recvWindow=5000   
&timestamp=1611825601400


#### Example 3: Mixed query string and request body

> **Example 3**

> **HMAC SHA256 signature:**

```shell
   $ echo -n "symbol=BTC-210129-40000-C&side=BUY&type=LIMIT&timeInForce=GTCquantity=0.01&price=2000&recvWindow=5000&timestamp=1611825601400" | openssl dgst -sha256 -hmac "YtP1BudNOWZE1ag5uzCkh4hIC7qSmQOu797r5EJBFGhxBYivjj8HIX0iiiPof5yG"
    (stdin)= fa6045c54fb02912b766442be1f66fab619217e551a4fb4f8a1ee000df914d8e
    
```

> **curl command:**

```shell
    (HMAC SHA256)
    $ curl -H "X-MBX-APIKEY: 22BjeOROKiXJ3NxbR3zjh3uoGcaflPu3VMyBXAg8Jj2J1xVSnY0eB4dzacdE9IWn" -X POST 'https://vapi.binance.com/vapi/v1/order?symbol=BTC-210129-40000-C&side=BUY&type=LIMIT&timeInForce=GTC' -d 'quantity=0.01&price=2000&recvWindow=5000&timestamp=1611825601400&signature=fa6045c54fb02912b766442be1f66fab619217e551a4fb4f8a1ee000df914d8e'
```

* **queryString:** 

symbol=BTC-210129-40000-C&side=BUY&type=LIMIT&timeInForce=GTC

* **requestBody:** 

quantity=0.01&price=2000&recvWindow=5000&timestamp=1611825601400


Note that the signature is different in example 3.
There is no & between "GTC" and "quantity=1".


## Public API Definitions

### Terminology

* `symbol` refers to the symbol name of a options contract symbol
* `underlying` refers to the underlying symbol of a options contract symbol
* `baseAsset` refers to the asset that is the quantity of a underlying.
* `quoteAsset` refers to the asset that is the price of a symbol.
* `settleAsset` refers to the settlement asset when options are exercised

### ENUM definitions

#### Options contract type

* CALL
* PUT

#### Order side

* BUY
* SELL

#### Position side

* LONG
* SHORT

#### Time in force

* GTC - Good Till Cancel
* IOC - Immediate or Cancel
* FOK - Fill or Kill 

#### Response Type (newOrderRespType)

* ACK
* RESULT

#### Order types (type)

* LIMIT
* MARKET
* REDUCING
* REDUCED
* LIQUIDATION

#### Order status (status)

* RECEIVED(0)
* UNTRIGGERED(1) 
* ACCEPTED(2)
* REJECTED(3)
* PARTIALLY_FILLED(4)
* FILLED(5)
* CANCELLING(6)
* CANCELLED(7)


# Quoting interface

## Test connectivity

>**Response:**

```javascript
{
  "code": 0,
  "msg": "success"
}
```

`GET /vapi/v1/ping`

**Weight:**
1

**Parameters:**
NONE



## Get server time

>**Response:**

```javascript
{
  "code": 0,
  "msg": "success",
  "data": 1592387156596 // Current system time
}
```

`GET /vapi/v1/time`

**Weight:**
1

**Parameters:**
NONE



## Get current trading pair info

>**Response:**

```javascript
{
  "code": 0,
  "msg": "success",
  "data": [
    {
      "id": 1,
      "contractId": 1,
      "underlying": "BTCUSDT",          // Underlying asset of the contract
      "quoteAsset": "USDT",             // Quotation asset
      "symbol": "BTC-200730-9000-C",    // Trading pair name
      "unit": 1,                        // Conversion ratio, the quantity of the underlying asset represented by a single contract.
      "minQty": 1,                      // Minimum trade volume of the underlying asset
      "side": "CALL",                   // Direction: CALL long, PUT short
      "leverage": 0,                    // Leverage
      "strikePrice": 9000,              // Strike price
      "expiryDate": 1593518400000       // Exercise time
    }
  ]
}
```

`GET /vapi/v1/optionInfo`

**Weight:**
1

**Parameters:**
NONE



## Get current limit info and trading pair info

>**Response:**

```javascript
{
  "code": 0,
  "msg": "success",
  "data": {
    "timezone": "UTC",                    // Time zone used by the server
    "serverTime": 1592387337630,          // Current system time
    "optionContracts": [                  // Option contract underlying asset info
      {
        "id": 1,
        "baseAsset": "BTC",               // Base currency
        "quoteAsset": "USDT",             // Quotation asset
        "underlying": "BTCUSDT",          // Name of the underlying asset of the option contract
        "settleAsset": "USDT"             // Settlement currency
      }
    ],
    "optionAssets": [                     // Option asset inf
      {
        "id": 1,
        "name": "USDT"                    // Asset name
      }
    ],
    "optionSymbols": [                    // Option trading pair info
      {
        "id": 1,
        "contractId": 1,
        "underlying": "BTCUSDT",          // Underlying asset of the contract
        "quoteAsset": "USDT",             // Quotation asset
        "symbol": "BTC-200730-9000-C",    // Trading pair name
        "unit": 1,                        // Conversion ratio, the quantity of the underlying asset represented by a single contract.
        "minQty": 1,                      // Minimum trade volume of the underlying asset
        "side": "CALL",                   // Direction: CALL long, PUT short
        "leverage": 0,                    // Leverage
        "strikePrice": 9000,              // Strike price
        "expiryDate": 1593518400000       // Exercise time
      }
    ]
  }
}
```

`GET /vapi/v1/exchangeInfo`

**Weight:**
1

**Parameters:**
NONE



## Get the spot index price

>**Response:**

```javascript
{
  "code": 0,
  "msg": "success",
  "data": {
    "indexPrice": "9200" // Current spot index price
  }
}
```

`GET /vapi/v1/index`

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description |  Demo
------------ | ------------ | ------------ | ------------ | ------------ 
underlying | STRING | YES | Spot pair（Option contract underlying asset） | BTCUSDT



## Get the latest price

>**Response:**

```javascript
{
  "code": 0,
  "msg": "success",
  "data": [
    {
      "symbol": "BTC-200730-9000-C",
      "priceChange": "-16.2038",        //24-hour price change
      "priceChangePercent": "-0.0162",  //24-hour percent price change
      "lastPrice": "1000",              //Last trade price
      "lastQty": "1000",                //Last trade amount
      "open": "1016.2038",              //24-hour open price
      "high": "1016.2038",              //24-hour high
      "low": "0",                       //24-hour low
      "volume": "5",                    //Trading volume
      "amount": null,                   //Trade amount
      "openTime": 1592317127349,        //Time the first trade occurred within the last 24 hours
      "closeTime": 1592380593516,       //Time the last trade occurred within the last 24 hours      "firstTradeId": 1,                //First trade ID
      "tradeCount": 5,                  //Number of trades
      "strikePrice": "9000"             //Strike price
    }
  ]
}
```

`GET /vapi/v1/ticker`

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description | Demo
------------ | ------------ | ------------ | ------------ | ------------ 
symbol | STRING | NO | Option trading pair | BTC-200730-9000-C



## Get the latest mark price

>**Response:**

```javascript
{
  "code": 0,
  "msg": "success",
  "data": [
    {
      "symbol": "BTC-200730-9000-C",
      "markPrice": 1343.2883,       // Mark price
      "bidIV": "1.40000077",        // Implied volatility Buy
      "askIV": "1.50000153",        // Implied volatility Sell
      "delta": 0.55937056,          // delta
      "theta": 3739.82509871,       // theta
      "gamma": 0.00010969,          // gamma
      "vega": 978.58874732,         // vega
      "highPriceLimit": 1618.241,   // Current highest buy price
      "lowPriceLimit": 1068.3356    // Current lowest sell price
    }
  ]
}
```

`GET /vapi/v1/mark`

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description | Demo
------------ | ------------ | ------------ | ------------ | ------------ 
symbol | STRING | NO | Option trading pair | BTC-200730-9000-C



## Depth information

>**Response:**

```javascript
{
  "code": 0,
  "msg": "success",
  "data": {
    "bids": [   // Buy order
      [
        "1000", // Price
        "0.9"   // Quantity
      ]
    ],
    "asks": [   // Sell order
      [
        "1100", // Price
        "0.1"   // Quantity
      ]
    ]
  }
}
```

`GET /vapi/v1/depth`

**Weight:**
5

**Parameters:**

Name | Type | Mandatory | Description | Demo
------------ | ------------ | ------------ | ------------ | ------------ 
symbol | STRING | YES | Option trading pair | BTC-200730-9000-C
limit | INT | NO | Default:100 Max:1000.Optional value:[10, 20, 50, 100, 500, 1000] | 100



## Candle data

>**Response:**

```javascript
{
  "code": 0,
  "msg": "success",
  "data": [
      {
          "close": "1000",            // Closing price (latest price if the current candle has not closed)
          "closeTime": 1499644799999, // Closing time
          "tradeCount": 10,           // Number of completed trades
          "high": "1100",             // Highest price
          "low": "900",               // Lowest price
          "open": "950",              // Opening price
          "openTime": 1499040000000,  // Opening time
          "takerAmount": "10000",     // Taker trade amount
          "takerVolume": "100",       // Taker trading volume
          "interval": "5m",           // Candle type
          "volume": "100"             // Trading volume        
      }
  ]
}
```

`GET /vapi/v1/klines`

**Weight:**
5

**Parameters:**

Name | Type | Mandatory | Description | Demo
------------ | ------------ | ------------ | ------------ | ------------ 
symbol | STRING | YES | Option trading pair | BTC-200730-9000-C
interval | STRING | YES | Time interval | 5m
startTime | LONG | NO | Start Time | 1592317127349
endTime | LONG | NO | End Time | 1592318127349
limit | INT | NO | Number of records Default:500 Max:1500 | 500



## Recently completed Option trades

>**Response:**

```javascript
{
  "code": 0,
  "msg": "success",
  "data": [
    {
      "price": "1000",      // Completed trade price
      "qty": "-0.1",        // Completed trade quantity
      "quoteQty": "-100",   // Completed trade amount
      "time": 1592449455993,// Time
      "side": -1            // Completed trade direction（-1 Sell，1 Buy）
    }
  ]
}
```

`GET /vapi/v1/trades`

**Weight:**
5

**Parameters:**

Name | Type | Mandatory | Description | Demo
------------ | ------------ | ------------ | ------------ | ------------ 
symbol | STRING | YES | Option trading pair | BTC-200730-9000-C
limit | INT | NO | Number of records Default:100 Max:500 | 100



## Query trade history

>**Response:**

```javascript
{
  "code": 0,
  "msg": "success",
  "data": [
    {
      "price": "1000",      // Completed trade price
      "qty": "-0.1",        // Completed trade Quantity
      "quoteQty": "-100",   // Completed trade amount
      "time": 1592449455993,// Time
      "side": -1            // Completed trade direction（-1 Sell，1 Buy）
    }
  ]
}
```

`GET /vapi/v1/historicalTrades`

**Weight:**
5

**Parameters:**

Name | Type | Mandatory | Description | Demo
------------ | ------------ | ------------ | ------------ | ------------ 
symbol | STRING | YES | Option trading pair | BTC-200730-9000-C
fromId | LONG | NO | The deal ID from which to return. The latest deal record is returned by default | 1592317127349
limit | INT | NO | Number of records Default:100 Max:500 | 100




# Websocket account info push

## Generate Listen Key (USER_STREAM)


>**Response:**

```javascript
{
  "code": 0,
  "msg": "success",
  "data": {
    "listenKey": "eBjOsjYmu1WD6oZHCRpMeWm4UzMvQelhOc9yTD4c"
  }
}
```

`POST /vapi/v1/userDataStream  (HMAC SHA256)`

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description |  Demo
------------ | ------------ | ------------ | ------------ | ------------ 
recvWindow | LONG | NO |  | 
timestamp | LONG | YES |  | 



## Extend Listen Key validity period (USER_STREAM)

>**Response:**

```javascript
{
  "code": 0,
  "msg": "success"
}
```

`PUT /vapi/v1/userDataStream`

Validity period has been extended by 60 minutes after this call

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description |  Demo
------------ | ------------ | ------------ | ------------ | ------------ 
listenKey | STRING | YES | Listen Key | eBjOsjYmu1WD6oZHCRpMeWm4UzMvQelhOc9yTD4c
recvWindow | LONG | NO |  | 
timestamp | LONG | YES |  | 



## Disable Listen Key (USER_STREAM)

>**Response:**

```javascript
{
  "code": 0,
  "msg": "success"
}
```

`DELETE /vapi/v1/userDataStream  (HMAC SHA256)`

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description |  Demo
------------ | ------------ | ------------ | ------------ | ------------ 
recvWindow | STRING | YES | Listen Key | eBjOsjYmu1WD6oZHCRpMeWm4UzMvQelhOc9yTD4c
recvWindow | LONG | NO |  | 
timestamp | LONG | YES |  | 




# Account and trading interface

## Account asset info (USER_DATA)

>**Response:**

```javascript
{
  "code": 0,
  "msg": "success",
  "data": [
    {
      "currency": "USDT",               // Asset type
      "equity": 10094.44662,            // Account equity
      "available": 8725.92524,          // Available funds
      "orderMargin": 1084.52138,        // Order margin
      "positionMargin": 289.00138,      // Position margin
      "unrealizedPNL": -5.00138,        // Unrealized profit/loss
      "maintMargin": 151.00138,         // Maintenance margin
      "balance": 10099.448              // Account balance
    }
  ]
}
```

`GET /vapi/v1/account  (HMAC SHA256)`

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description |  Demo
------------ | ------------ | ------------ | ------------ | ------------ 
recvWindow | LONG | NO |  | 
timestamp | LONG | YES |  | 



## Funds transfer (USER_DATA)

>**Response:**

```javascript
{
  "code": 0,
  "msg": "success",
  "data": 4         //  Transfer record ID
}
```

`POST /vapi/v1/transfer  (HMAC SHA256)`

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description |  Demo
------------ | ------------ | ------------ | ------------ | ------------ 
currency | STRING | YES | Asset type | USDT
type | ENUM | YES | IN: Transfer from spot account to option account OUT: Transfer from option account to spot account | IN
amount | DECIMAL | YES | Amount | 10000
recvWindow | LONG | NO |  | 
timestamp | LONG | YES |  | 



## Option holdings info (USER_DATA)

>**Response:**

```javascript
{
  "code": 0,
  "msg": "success",
  "data": [
    {
      "entryPrice": 1000,               // Average entry price
      "symbol": "BTC-200730-9000-C",    // Option trading pair
      "side": "SHORT",                  // Position direction
      "leverage": 0,                    // Leverage
      "quantity": -0.1,                 // Number of positions (positive numbers represent long positions, negative number represent short positions)
      "reducibleQty": 0,                // Number of positions that can be reduced
      "markValue": 105.00138,           // Current market value
      "autoReducePriority": 0,          // Auto-deleverage ranking
      "ror": -0.05,                     // Rate of return
      "unrealizedPNL": -5.00138,        // Unrealized profit/loss
      "markPrice": 1050.0138,           // Mark price
      "strikePrice": 9000,              // Strike price
      "expiryDate": 1593511200000       // Exercise time
    }
  ]
}
```

`GET /vapi/v1/position  (HMAC SHA256)`

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description |  Demo
------------ | ------------ | ------------ | ------------ | ------------ 
symbol | STRING | NO | Option trading pair | BTC-200730-9000-C
recvWindow | LONG | NO |  | 
timestamp | LONG | YES |  | 



## Account funding flow (USER_DATA)

>**Response:**

```javascript
{
  "code": 0,
  "msg": "success",
  "data": [
    {
      "id": 1125899906842624000,
      "currency": "USDT",           // Asset type
      "amount": -0.552,             // Amount (positive numbers represent inflow, negative numbers represent outflow)
      "type": "FEE",                // Account type (fees)
      "createDate": 1592449456000,  // Time
    },
    {
      "id": 1125899906842624000,
      "currency": "USDT",           // Asset type
      "amount": 100,                // Amount (positive numbers represent inflow, negative numbers represent outflow)
      "type": "CONTRACT",           // Account type (buy/sell contracts)
      "createDate": 1592449456000,  // Time
    },
    {
      "id": 1125899906842624000,
      "currency": "USDT",           // Asset type
      "amount": 10000,              // Amount (positive numbers represent inflow, negative numbers represent outflow)
      "type": "TRANSFER",           // Account type（Funds transfer）
      "createDate": 1592448410000,  // Time
    }
  ]
}
```

`POST /vapi/v1/bill  (HMAC SHA256)`

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description |  Demo
------------ | ------------ | ------------ | ------------ | ------------ 
currency | STRING | YES | Asset type | USDT
recordId | LONG | NO | Return the recordId and subsequent data, the latest data is returned by default | 100000
startTime | LONG | NO | Start Time | 1593511200000
endTime | LONG | NO | End Time | 1593512200000
limit | INT | NO | Number of result sets returned Default:100 Max:1000 | 100
recvWindow | LONG | NO |  | 
timestamp | LONG | YES |  | 



## Option order (TRADE)

>**Response:**

```javascript
{
  "code": 0,
  "msg": "success",
  "data": {
    "id": "4611875134427365377",        // System order number
    "symbol": "BTC-200730-9000-C",      // Option trading pair
    "price": 100,                       // Order Price
    "quantity": 1,                      // Order Quantity
    "executedQty": 0,                   // Number of completed trades
    "fee": 0,                           // Fee 
    "side": "BUY",                      // Buy/sell direction
    "type": "LIMIT",                    // Order type
    "timeInForce": "GTC",               // Time in force method
    "createDate": 1592465880683,        // Order Time
    "status": "ACCEPTED",               // Order status
    "avgPrice": 0,                      // Average price of completed trade
    "source": "WEB",                    // Order source
    "reduceOnly": false,                // Order is reduce only Y/N
    "clientOrderId": ""                 // Client order ID
  }
}
```

`POST /vapi/v1/order  (HMAC SHA256)`

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description |  Demo
------------ | ------------ | ------------ | ------------ | ------------ 
symbol | STRING | YES | Option trading pair | BTC-200730-9000-C
side | ENUM | YES | Buy/sell direction: SELL, BUY | BUY
type | ENUM | YES | Order Type: LIMIT, MARKET | LIMIT
quantity | DECIMAL | YES | Order Quantity | 3
price | DECIMAL | NO | Order Price | 1000
timeInForce | ENUM | NO | Time in force method（Default GTC） | GTC
reduceOnly | BOOLEAN | NO | Reduce Only（Default false） | false
newOrderRespType | ENUM | NO | "ACK", "RESULT", Default "ACK" | ACK
clientOrderId | STRING | NO | User-defined order ID cannot be repeated in pending orders | 10000
recvWindow | LONG | NO |  | 
timestamp | LONG | YES |  | 

Some parameters are mandatory depending on the order type as follows:

Type | Mandatory parameters
------------ | ------------ 
LIMIT | timeInForce, quantity, price
MARKET | quantity




## Place Multiple Option orders (TRADE)

>**Response:**

```javascript
{
  "code": 0,
  "msg": "SUCCESS 5, FAILED 0",
  "data": [{
    "id": "4611875134427365377",        // System order number
    "symbol": "BTC-200730-9000-C",      // Option trading pair
    "price": 100,                       // Order Price
    "quantity": 1,                      // Order Quantity
    "executedQty": 0,                   // Number of completed trades
    "fee": 0,                           // Fee 
    "side": "BUY",                      // Buy/sell direction
    "type": "LIMIT",                    // Order type
    "timeInForce": "GTC",               // Time in force method
    "createDate": 1592465880683,        // Order Time
    "status": "ACCEPTED",               // Order status
    "avgPrice": 0,                      // Average price of completed trade
    "source": "WEB",                    // Order source
    "reduceOnly": false,                // Order is reduce only Y/N
    "clientOrderId": ""                 // Client order ID
  }]
}
```

`POST /vapi/v1/batchOrders  (HMAC SHA256)`

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description |  Demo
------------ | ------------ | ------------ | ------------ | ------------ 
orders | LIST | YES | order list. Max 5 orders | [{"symbol":"BTC-210115-35000-C","price":"100","quantity":"0.0001","side":"BUY","type":"LIMIT"}]
recvWindow | LONG | NO |  | 
timestamp | LONG | YES |  | 

**Where batchOrders is the list of order parameters in JSON:**

Name | Type | Mandatory | Description |  Demo
------------ | ------------ | ------------ | ------------ | ------------ 
symbol | STRING | YES | Option trading pair | BTC-200730-9000-C
side | ENUM | YES | Buy/sell direction: SELL, BUY | BUY
type | ENUM | YES | Order Type: LIMIT, MARKET | LIMIT
quantity | DECIMAL | YES | Order Quantity | 3
price | DECIMAL | NO | Order Price | 1000
timeInForce | ENUM | NO | Time in force method（Default GTC） | GTC
reduceOnly | BOOLEAN | NO | Reduce Only（Default false） | false
newOrderRespType | ENUM | NO | "ACK", "RESULT", Default "ACK" | ACK
clientOrderId | STRING | NO | User-defined order ID cannot be repeated in pending orders | 10000

Some parameters are mandatory depending on the order type as follows:

Type | Mandatory parameters
------------ | ------------ 
LIMIT | timeInForce, quantity, price
MARKET | quantity


* Paremeter rules are same with New Order
* Batch orders are processed concurrently, and the order of matching is not guaranteed.
* The order of returned contents for batch orders is the same as the order of the order list.




## Cancel Option order (TRADE)

>**Response:**

```javascript
{
  "code": 0,
  "msg": "success",
  "data": {
    "id": "4611875134427365377",        // System order number
    "symbol": "BTC-200730-9000-C",      // Option trading pair
    "price": 100,                       // Order Price
    "quantity": 1,                      // Order Quantity
    "executedQty": 0,                   // Number of completed trades
    "fee": 0,                           // Fee 
    "side": "BUY",                      // Buy/sell direction
    "type": "LIMIT",                    // Order type
    "timeInForce": "GTC",               // Time in force method
    "createDate": 1592465880683,        // Order Time
    "status": "ACCEPTED",               // Order status
    "avgPrice": 0,                      // Average price of completed trade
    "source": "WEB",                    // Order source
    "reduceOnly": false,                // Order is reduce only Y/N
    "clientOrderId": ""                 // Client order ID
  }
}
```

`DELETE /vapi/v1/order  (HMAC SHA256)`

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description |  Demo
------------ | ------------ | ------------ | ------------ | ------------ 
symbol | STRING | YES | Option trading pair | BTC-200730-9000-C
orderId | LONG | NO | Order ID | 4611875134427365377
clientOrderId | STRING | NO | User-defined order ID | 10000
recvWindow | LONG | NO |  | 
timestamp | LONG | YES |  | 

At least one instance of orderId and clientOrderId must be sent.




## Cancel Multiple Option orders (TRADE)

>**Response:**

```javascript
{
  "code": 0,
  "msg": "SUCCESS 5, FAILED 0",
  "data": [{
    "id": "4611875134427365377",        // System order number
    "symbol": "BTC-200730-9000-C",      // Option trading pair
    "price": 100,                       // Order Price
    "quantity": 1,                      // Order Quantity
    "executedQty": 0,                   // Number of completed trades
    "fee": 0,                           // Fee 
    "side": "BUY",                      // Buy/sell direction
    "type": "LIMIT",                    // Order type
    "timeInForce": "GTC",               // Time in force method
    "createDate": 1592465880683,        // Order Time
    "status": "ACCEPTED",               // Order status
    "avgPrice": 0,                      // Average price of completed trade
    "source": "WEB",                    // Order source
    "reduceOnly": false,                // Order is reduce only Y/N
    "clientOrderId": ""                 // Client order ID
  }]
}
```

`DELETE /vapi/v1/batchOrders  (HMAC SHA256)`

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description |  Demo
------------ | ------------ | ------------ | ------------ | ------------ 
symbol | STRING | YES | Option trading pair | BTC-200730-9000-C
orderIds | LIST<LONG> | NO | Order ID | [4611875134427365377,4611875134427365378]
clientOrderIds | LIST<STRING> | NO | User-defined order ID | ["my_id_1","my_id_2"]
recvWindow | LONG | NO |  | 
timestamp | LONG | YES |  | 

Either orderIds or origClientOrderIds must be sent.




## Cancel all Option orders (TRADE)

>**Response:**

```javascript
{
  "code": 0,
  "msg": "success"
}
```

`DELETE /vapi/v1/allOpenOrders (HMAC SHA256)`

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description |  Demo
------------ | ------------ | ------------ | ------------ | ------------ 
symbol | STRING | YES | Option trading pair | BTC-200730-9000-C
recvWindow | LONG | NO |  | 
timestamp | LONG | YES |  | 



## Query Option order (TRADE)

>**Response:**

```javascript
{
  "code": 0,
  "msg": "success",
  "data": {
    "id": "4611875134427365377",        // System order number
    "symbol": "BTC-200730-9000-C",      // Option trading pair
    "price": 100,                       // Order Price
    "quantity": 1,                      // Order Quantity
    "executedQty": 0,                   // Number of completed trades
    "fee": 0,                           // Fee 
    "side": "BUY",                      // Buy/sell direction
    "type": "LIMIT",                    // Order type
    "timeInForce": "GTC",               // Time in force method
    "createDate": 1592465880683,        // Order Time
    "status": "ACCEPTED",               // Order status
    "avgPrice": 0,                      // Average price of completed trade
    "source": "WEB",                    // Order source
    "reduceOnly": false,                // Order is reduce only Y/N
    "clientOrderId": ""                 // Client order ID
  }
}
```

`GET /vapi/v1/order (HMAC SHA256)`

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description |  Demo
------------ | ------------ | ------------ | ------------ | ------------ 
symbol | STRING | YES | Option trading pair | BTC-200730-9000-C
orderId | LONG | NO | Order ID | 4611875134427365377
clientOrderId | STRING | NO | User-defined order ID | 10000
recvWindow | LONG | NO |  | 
timestamp | LONG | YES |  | 

At least one instance of orderId and clientOrderId must be sent.



## Query current pending Option orders (TRADE)

>**Response:**

```javascript
{
  "code": 0,
  "msg": "success",
  "data": [
    {
      "id": "4611875134427365377",        // System order number
      "symbol": "BTC-200730-9000-C",      // Option trading pair
      "price": 100,                       // Order Price
      "quantity": 1,                      // Order Quantity
      "executedQty": 0,                   // Number of completed trades
      "fee": 0,                           // Fee 
      "side": "BUY",                      // Buy/sell direction
      "type": "LIMIT",                    // Order type
      "timeInForce": "GTC",               // Time in force method
      "createDate": 1592465880683,        // Order Time
      "status": "ACCEPTED",               // Order status
      "avgPrice": 0,                      // Average price of completed trade
      "source": "WEB",                    // Order source
      "reduceOnly": false,                // Order is reduce only Y/N
      "clientOrderId": ""                 // Client order ID
    }
  ]
}
```

`GET /vapi/v1/openOrders  (HMAC SHA256)`

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description |  Demo
------------ | ------------ | ------------ | ------------ | ------------ 
symbol | STRING | YES | Option trading pair | BTC-200730-9000-C
orderId | LONG | NO | Returns the orderId and subsequent orders, the most recent order is returned by default | 100000
startTime | LONG | NO | Start Time | 1593511200000
endTime | LONG | NO | End Time | 1593512200000
limit | INT | NO | Number of result sets returned Default:100 Max:1000 | 100
recvWindow | LONG | NO |  | 
timestamp | LONG | YES |  | 



## Query Option order history (TRADE)

>**Response:**

```javascript
{
  "code": 0,
  "msg": "success",
  "data": [
    {
      "id": "4611875134427365377",        // System order number
      "symbol": "BTC-200730-9000-C",      // Option trading pair
      "price": 100,                       // Order Price
      "quantity": 1,                      // Order Quantity
      "executedQty": 0,                   // Number of completed trades
      "fee": 0,                           // Fee 
      "side": "BUY",                      // Buy/sell direction
      "type": "LIMIT",                    // Order type
      "timeInForce": "GTC",               // Time in force method
      "createDate": 1592465880683,        // Order Time
      "status": "CANCELLED",              // Order status
      "avgPrice": 0,                      // Average price of completed trade
      "source": "WEB",                    // Order source
      "reduceOnly": false,                // Order is reduce only Y/N
      "clientOrderId": ""                 // Client order ID
    }
  ]
}
```

`GET /vapi/v1/historyOrders  (HMAC SHA256)`

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description |  Demo
------------ | ------------ | ------------ | ------------ | ------------ 
symbol | STRING | YES | Option trading pair | BTC-200730-9000-C
orderId | LONG | NO | Returns the orderId and subsequent orders, the most recent order is returned by default | 100000
startTime | LONG | NO | Start Time | 1593511200000
endTime | LONG | NO | End Time | 1593512200000
limit | INT | NO | Number of result sets returned Default:100 Max:1000 | 100
recvWindow | LONG | NO |  | 
timestamp | LONG | YES |  | 





# Push Websocket account info

 Base URL of the API interface listed in this article: **https://vapi.binance.com** 
 A User Data Stream listenKey is valid for 60 minutes after creation.
 You can extend the validity period by 60 minutes by performing a PUT listenKey operation.
 You can immediately terminate the current data stream by performing a DELETE listenKey operation, which will render that listenKey invalid.
 Performing a POST operation on an account with a valid listenKey will return the current valid listenKey and extend its validity period by 60 minutes. 
 The baseurl of the websocket interface is: **wss://vstream.binance.com** 
 User Data Streams are accessed at /ws/ or /stream?streams=
 Each link can be valid for no more than 24 hours. Please be sure to handle disconnection/reconnection appropriately.
 Account data stream info is not guaranteed to be in strict chronological order. Please use the E field for sorting.
 
 Data is compressed as a GZIP file by default. After successful connection, send {"method":"BINARY", "params":["false"], "id":1} to convert to text data.

## Generate Listen Key (USER_STREAM)
Start a new data stream. The data stream will be terminated after 60 minutes unless sending keepalive. If the account has a valid listenKey, the listenKey will be returned and the validity period will be extended by 60 minutes.

>**Response:**

```javascript
{
  "listenKey":"pqia91ma19a5s61cv6a81va65sdf19v8a65a1a5s61cv6a81va65sdf19v8a65a1"
}
```


`GET /vapi/v1/userDataStream`

**Weight:**
1

**Parameters:**
NONE




## Extend Listen Key validity period (USER_STREAM)
The validity period will be extended until 60 minutes after this call. It is recommended to send a put request every 30 minutes.

>**Response:**

```javascript
{
  "listenKey":"pqia91ma19a5s61cv6a81va65sdf19v8a65a1a5s61cv6a81va65sdf19v8a65a1"
}
```

`PUT /vapi/v1/userDataStream`

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description |  Demo
------------ | ------------ | ------------ | ------------ | ------------ 
listenKey | STRING | YES | listenKey | "pqia91ma19a5s61cv6a81va65sdf19v8a65a1a5s61cv6a81va65sdf19v8a65a1"


## Delete Listen Key (USER_STREAM)
Start a new data stream. The data stream will be terminated after 60 minutes unless sending keepalive. If the account has a valid listenKey, the listenKey will be returned and the validity period will be extended by 60 minutes.

>**Response:**

```javascript
{}
```

`DELETE /vapi/v1/userDataStream`

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description |  Demo
------------ | ------------ | ------------ | ------------ | ------------ 
listenKey | STRING | YES | listenKey | "pqia91ma19a5s61cv6a81va65sdf19v8a65a1a5s61cv6a81va65sdf19v8a65a1"



## Payload: Account data
Update under the following conditions: Account recharge or withdrawal
A transfer occurs between trading accounts (e.g. transfer from spot to margin accounts)
Position changes


>**Response:**

```javascript
{
    "e":"ACCOUNT_UPDATE", event type
    "E":1591696384141, event time
    "B":[
        {
          
            "b":"100007992.26053177",       // Account balance
            "m":"0",                        // Position value
            "u":"458.782655111111",         // Unrealized profit/loss
            "U":"458.782655111111",         // Unrealized profit/loss (Seller)
            "o":"-13238.246342",            // Order margin
            "p":"-18852.328456",            // Position margin
            "r":"-15452.328456",            // Reduce margin
            "M":"-15452.328456"             // Maintenance margin
        }
    ],
    // Position info change. Includes a P attribute if there are changes, otherwise does not include a P attribute.
    "P":[
          {
           "S": symbol,                     // Contract type
           "c":1,                           // Number of current positions
           "r":1,                           // Number of positions that can be reduced
           "p":1,                           // Position value
           "a":1                            // Average entry price
          }
        ]
}

```
## Payload: Order data
Update under the following conditions: Order change, order placed, order cancelled, trade completed 

### Order status (s)

* 0 RECEIVED
* 1 UNTRIGGERED 
* 2 ACCEPTED
* 3 REJECTED
* 4 PARTIALLY_FILLED
* 5 FILLED
* 6 CANCELLING
* 7 CANCELLED

>**Response:**

```javascript
{
 "e":"ORDER_TRADE_UPDATE",          // event type
 "E":1591698525864,                 // event time
 "o":[
     {
         "T":0,                     // trade time 
         "oid":1,                   // Order ID
         "S":symbol,                // contract name
         "cid":"zdy",               // user-defined info
         "q":"0.111",               // Trading volume
         "stp":0,                   // selfTradePrevention
         "r":false,                 // reduce only
         "c":false,                 // temporarily unused
         "s":0,                     // status
         "e":"1",                   // completed trade volume
         "ec":"1",                  // completed trade amount
         "f":"0.011"                // Fee 
     }
 ]
}
```
## Market Streams Payload: 24-hour TICKER
### Subscribe to an info stream

>**Response:**

```javascript
{
   "e":"ticker",                // event type
   "E":1591677962357,           // event time
   "s":"BTC-200630-9000-P",     // Option trading pair
   "o":"1000",                  // 24-hour opening price 
   "h":"1000",                  // Highest price
   "l":"1000",                  // Lowest price
   "c":"1000",                  // latest price
   "V":"2",                     // Trading volume
   "A":"0",                     // trade amount
   "p":"0",                     // price change
   "Q":"2000",                  // volume of last completed trade
   "F":1,                       // first trade ID
   "L":1,                       // last trade ID
   "n":1,                       // number of trades
   "b":"0",                     // BuyImplied volatility
   "a":"0",                     // SellImplied volatility
   "d":"0",                     // delta
   "t":"0",                     // theta
   "g":"0",                     // gamma
   "v":"0"                      // vega
}
```

**Request**

{    
"method": "SUBSCRIBE",   
"params":   
[    
"BTC-200630-9000-P@ticker"    
],   
"id": 1  
}

24ticker


## Market Streams Payload: Latest completed trades

>**Response:**

```javascript
{
    "e":"trade",                        // event type
    "E":1591677941092,                  // event time
    "s":"BTC-200630-9000-P",            // Option trading pair
    "t":[                               // trade history
        {
            "t":1,                      // trade ID
            "p":"1000",                 // trade price
            "q":"-2",                   // Trading volume
            "b":4611781675939004417,    // buy order ID
            "a":4611781675939004418,    // sell order ID
            "T":1591677567872,          // trade completed time
            "s":"-1"                    // direction
        }
    ]
}
```

**Request**

{    
"method": "SUBSCRIBE",   
"params":   
[    
"BTC-200630-9000-P@trade"    
],   
"id": 1    
}





## Market Streams Payload: Candle

**Request**

{   
"method": "SUBSCRIBE",   
"params":   
[     
"BTC-200630-9000-P@kline_1m"    
],   
"id": 1   
}


**Cycle**

"1m", 
"3m", 
"5m", 
"15m"
"30m"
"1h",
"2h",
"4h",
"6", 
"12h
"1d", 
"3d", 
"1w",

>**Response:**

```javascript
{
    "e":"kline",                        // event type
    "E":1591677941085,                  // event time
    "s":"BTC-200630-9000-P",            // Option trading pair
    "k":[{                              // second to last
        "t":1591677900000,              // candle event
        "i":"1m",                       // candle period
        "F":0,                          // first trade ID
        "L":0,                          // last trade ID
        "o":"1000",                     // open
        "h":"1000",                     // high
        "l":"1000",                     // low
        "c":"1000",                     // close
        "v":"0",                        // volume
        "n":0,                          // number of trades
        "q":"0",                        // completed trade amount
        "x":false,                      // current candle has been completed Y/N
        "V":"0",                        // taker completed trade volume
        "Q":"0"                         // taker trade amount
    },{                                 // Latest candle data
        "t":1591677900000,              // candle event
        "i":"1m",                       // candle period
        "F":0,                          // first trade ID
        "L":0,                          // last trade ID
        "o":"1000",                     // open
        "h":"1000",                     // high    
        "l":"1000",                     // low
        "c":"1000",                     // close
        "v":"0",                        // volume
        "n":0,                          // number of trades
        "q":"0",                        // completed trade amount
        "x":false,                      // current candle has been completed Y/N
        "V":"0",                        // taker completed trade volume
        "Q":"0"                         // taker trade amount
    }
  ]
}
```
## Market Streams Payload: Depth

>**Response:** 1000 Diff. Depth Stream

```javascript
{
    "e":"depth",                    // event type
    "E":1591695934033,              // event time
    "s":"BTC-200630-9000-P",        // Option trading pair
    "b":[                           // Buyorder
        [
            "200",                  // Price
            "3",                    // volume
        ],
        [
            "101",
            "1"
        ],
        [
            "100",
            "2"
        ]
    ],
    "a":[                           // Sellorder
        [
            "1000",
            "89"
        ]
    ]
}
```

**Request**

{   
"method": "SUBSCRIBE",   
"params":   
[    
"BTC-200630-9000-P@depth10"   
],   
"id": 1   
}


* Cycle
"10", 
"20", 
"50",
"100"
"1000"



## How to manage a local order book correctly

1. Send a subscription depth request. The full data will be returned in the first response after subscribing. It needs to be cached locally.
2. The data in each event is the absolute quantity for a price level.
3. If the quantity is 0, remove the price level.
4. If a certain price does not exist in the local cache, please add a new local cache.
5. If the connection is disconnected, please reconnect, and then repeat steps 1-4.




**Request**

{     
"method": "SUBSCRIBE",     
"params":    
[     
"option_pair"     
],    
"id": 1      
}    


## Get current limit info and trading pair info
>**Response:**

```javascript
{
    "e":"OPTION_PAIR", //eventType
    "E":1611636498525, //eventTime
    "id":1,            //id 
    "cid":1,           //cid
    "u":"BTCUSDT",     // Underlying asset of the contract
    "qa":"USDT",       // Quotation asset
    "S":"BTC-210106-10000-C", //Trading pair name
    "unit":"1",        // Conversion ratio, the quantity of the underlying asset represented by a single contract.
    "mq":"0.0001",     // Minimum trade volume of the underlying asset
    "s":1,             // Direction: 1 long, -1 short
    "l":"0",           // Leverage
    "sp":"10000",      // Strike price
    "ed":1609927200000 //Exercise time
}
```



Websocket Error Messages

Error Messages | Description 
------------ | ------------ 
{"error":{"code":-1125,"msg":"Invalid ListenKey "}} | Invalid ListenKey 
{"error":{"code":-1130,"msg":"UNKNOWN_PARAM "}} | UNKNOWN_PARAM


# ErrorCodes

Errors consist of two parts: an error code and a message.
Codes are universal, but messages can vary.


## 10xx - General Server or Network issues
### -1000 UNKNOWN
* An unknown error occurred while processing the request.

### -1001 DISCONNECTED
* Internal error; unable to process your request. Please try again.

### -1002 UNAUTHORIZED
* You are not authorized to execute this request.

### -1003 TOO_MANY_REQUESTS
* Too many requests queued.
 * Too much request weight used; please use the websocket for live updates to avoid polling the API.
 * Too much request weight used; current limit is %s request weight per %s %s. Please use the websocket for live updates to avoid polling the API.
 * Way too much request weight used; IP banned until %s. Please use the websocket for live updates to avoid bans.

### -1004 SERVER_BUSY
* Server is busy, please wait and try again

### -1006 UNEXPECTED_RESP
* An unexpected response was received from the message bus. Execution status unknown.

### -1007 TIMEOUT
* Timeout waiting for response from backend server. Send status unknown; execution status unknown.

### -1014 UNKNOWN_ORDER_COMPOSITION
* Unsupported order combination.

### -1015 TOO_MANY_ORDERS
* Too many new orders.
* Too many new orders; current limit is %s orders per %s.

### -1016 SERVICE_SHUTTING_DOWN
* This service is no longer available.

### -1020 UNSUPPORTED_OPERATION
* This operation is not supported.

### -1021 INVALID_TIMESTAMP
* Timestamp for this request is outside of the recvWindow.
* Timestamp for this request was 1000ms ahead of the server's time.

### -1022 INVALID_SIGNATURE
* Signature for this request is not valid.


## 11xx - 2xxx Request issues
### -1100 ILLEGAL_CHARS
* Illegal characters found in a parameter.
* Illegal characters found in a parameter. %s
* Illegal characters found in parameter `%s`; legal range is `%s`.

### -1101 TOO_MANY_PARAMETERS
* Too many parameters sent for this endpoint.
* Too many parameters; expected `%s` and received `%s`.
* Duplicate values for a parameter detected.

### -1102 MANDATORY_PARAM_EMPTY_OR_MALFORMED
* A mandatory parameter was not sent, was empty/null, or malformed.
* Mandatory parameter `%s` was not sent, was empty/null, or malformed.
* Param `%s` or `%s` must be sent, but both were empty/null!

### -1103 UNKNOWN_PARAM
* An unknown parameter was sent.

### -1104 UNREAD_PARAMETERS
* Not all sent parameters were read.
* Not all sent parameters were read; read `%s` parameter(s) but was sent `%s`.

### -1105 PARAM_EMPTY
* A parameter was empty.
* Parameter `%s` was empty.

### -1106 PARAM_NOT_REQUIRED
* A parameter was sent when not required.
* Parameter `%s` sent when not required.

### -1111 BAD_PRECISION
* Precision is over the maximum defined for this asset.

### -1112 NO_DEPTH
* No orders on book for symbol.

### -1114 TIF_NOT_REQUIRED
* TimeInForce parameter sent when not required.

### -1115 INVALID_TIF
* Invalid timeInForce.

### -1116 INVALID_ORDER_TYPE
* Invalid orderType.

### -1117 INVALID_SIDE
* Invalid side.

### -1118 EMPTY_NEW_CL_ORD_ID
* New client order ID was empty.

### -1119 EMPTY_ORG_CL_ORD_ID
* Original client order ID was empty.

### -1120 BAD_INTERVAL
* Invalid interval.

### -1121 BAD_SYMBOL
* Invalid symbol.

### -1125 INVALID_LISTEN_KEY
* This listenKey does not exist.

### -1127 MORE_THAN_XX_HOURS
* Lookup interval is too big.
* More than %s hours between startTime and endTime.

### -1128 BAD_CONTRACT
* Invalid underlying

### -1129 BAD_CURRENCY
* Invalid asset。

### -1130 INVALID_PARAMETER
* Invalid data sent for a parameter.
* Data sent for paramter `%s` is not valid.

### -1131 BAD_RECV_WINDOW
* recvWindow must be less than 60000

### -2010 NEW_ORDER_REJECTED
* NEW_ORDER_REJECTED

### -2011 CANCEL_REJECTED
* CANCEL_REJECTED

### -2013 NO_SUCH_ORDER
* Order does not exist.

### -2014 BAD_API_KEY_FMT
* API-key format invalid.

### -2015 INVALID_API_KEY
* Invalid API-key, IP, or permissions for action.

### -2018 BALANCE_NOT_SUFFICIENT
* Balance is insufficient.

### -2027 OPTION_MARGIN_NOT_SUFFICIENT
* Option margin is insufficient.


## 3xxx-5xxx Filters and other issues

### -3029 TRANSFER_FAILED
* Asset transfer fail.

### -4001 PRICE_LESS_THAN_ZERO
* Price less than 0.

### -4002 PRICE_GREATER_THAN_MAX_PRICE
* Price greater than max price.

### -4003 QTY_LESS_THAN_ZERO
* Quantity less than zero.

### -4004 QTY_LESS_THAN_MIN_QTY
* Quantity less than min quantity.

### -4005 QTY_GREATER_THAN_MAX_QTY
* Quantity greater than max quantity.

### -4013 PRICE_LESS_THAN_MIN_PRICE
* Price less than min price.

### -4029 INVALID_TICK_SIZE_PRECISION
* Tick size precision is invalid.

### -4030 INVALID_QTY_PRECISION
* Step size precision is invalid.

### -4055 AMOUNT_MUST_BE_POSITIVE
* Amount must be positive.

### -5055 BLACK_COUNTRY
* This is a blacklist country.

