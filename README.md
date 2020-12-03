#  General Info

## General API Information

* The base endpoint is: **https://testnet.binanceops.com**
* All endpoints return either a JSON object or array.
* Data is returned in ascending order. Oldest first, newest last.
* All time and timestamp related fields are in milliseconds.

## General Information on Endpoints

* For GET endpoints, parameters must be sent as a query string.
* For POST, PUT, and DELETE endpoints, the parameters may be sent as a query string or in the request body with content type application/x-www-form-urlencoded. You may mix parameters between both the query string and request body if you wish to do so.
* Parameters may be sent in any order.
* If a parameter sent in both the query string and request body, the query string parameter will be used.

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

## Timing security

* A SIGNED endpoint also requires a parameter, timestamp, to be sent which should be the millisecond timestamp of when the request was created and sent.
* An additional parameter, recvWindow, may be sent to specify the number of milliseconds after timestamp the request is valid for. If recvWindow is not sent, it defaults to 5000.
* If the server determines that the timestamp sent by the client is more than one second in the future of the server time, the request will also be rejected.

**Serious trading is about timing. ** Networks can be unstable and unreliable, which can lead to requests taking varying amounts of time to reach the servers. With recvWindow, you can specify that the request must be processed within a certain number of milliseconds or be rejected by the server.
>It is recommended to use a small recvWindow of 5000 or less!


# Quoting interface

## Test connectivity

`GET /api/v1/ping`

**Weight:**
1

**Parameters:**
NONE

>**Response:**

```javascript
{
  "code": 0,
  "msg": "success"
}
```

## Get server time

`GET /api/v1/time`

**Weight:**
1

**Parameters:**
NONE

>**Response:**

```javascript
{
  "code": 0,
  "msg": "success",
  "data": 1592387156596 // Current system time
}
```

## Get current trading pair info

`GET /api/v1/optionInfo`

**Weight:**
1

**Parameters:**
NONE

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
      "symbol": "BTCUSDT-200730-9000-C",// Trading pair name
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

## Get current limit info and trading pair info

`GET /api/v1/exchangeInfo`

**Weight:**
1

**Parameters:**
NONE

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
        "symbol": "BTCUSDT-200730-9000-C",// Trading pair name
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

## Get the spot index price

`GET /api/v1/index`

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description |  Demo
------------ | ------------ | ------------ | ------------ | ------------ 
underlying | STRING | YES | Spot pair（Option contract underlying asset） | BTCUSDT

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

## Get the latest price

`GET /api/v1/ticker`

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description | Demo
------------ | ------------ | ------------ | ------------ | ------------ 
symbol | STRING | NO | Option trading pair | BTCUSDT-200730-9000-C

>**Response:**

```javascript
{
  "code": 0,
  "msg": "success",
  "data": [
    {
      "symbol": "BTCUSDT-200730-9000-C",
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

## Get the latest mark price

`GET /api/v1/mark`

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description | Demo
------------ | ------------ | ------------ | ------------ | ------------ 
symbol | STRING | NO | Option trading pair | BTCUSDT-200730-9000-C

>**Response:**

```javascript
{
  "code": 0,
  "msg": "success",
  "data": [
    {
      "symbol": "BTCUSDT-200730-9000-C",
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

## Depth information

`GET /api/v1/depth`

**Weight:**
5

**Parameters:**

Name | Type | Mandatory | Description | Demo
------------ | ------------ | ------------ | ------------ | ------------ 
symbol | STRING | YES | Option trading pair | BTCUSDT-200730-9000-C
limit | INT | NO | Default:100 Max:1000.Optional value:[5, 10, 20, 50, 100, 500, 1000] | 100

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

## Candle data

`GET /api/v1/klines`

**Weight:**
5

**Parameters:**

Name | Type | Mandatory | Description | Demo
------------ | ------------ | ------------ | ------------ | ------------ 
symbol | STRING | YES | Option trading pair | BTCUSDT-200730-9000-C
interval | STRING | YES | Time interval | 5m
startTime | LONG | NO | Start Time | 1592317127349
endTime | LONG | NO | End Time | 1592318127349
limit | INT | NO | Number of records Default:500 Max:1500 | 500

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

## Recently completed Option trades

`GET /api/v1/trades`

**Weight:**
5

**Parameters:**

Name | Type | Mandatory | Description | Demo
------------ | ------------ | ------------ | ------------ | ------------ 
symbol | STRING | YES | Option trading pair | BTCUSDT-200730-9000-C
limit | INT | NO | Number of records Default:100 Max:500 | 100

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

## Query trade history

`GET /api/v1/historicalTrades`

**Weight:**
5

**Parameters:**

Name | Type | Mandatory | Description | Demo
------------ | ------------ | ------------ | ------------ | ------------ 
symbol | STRING | YES | Option trading pair | BTCUSDT-200730-9000-C
fromId | LONG | NO | The deal ID from which to return. The latest deal record is returned by default | 1592317127349
limit | INT | NO | Number of records Default:100 Max:500 | 100

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


# Websocket account info push

## Generate Listen Key (USER_STREAM)

`POST /api/v1/userDataStream  (HMAC SHA256)`

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description |  Demo
------------ | ------------ | ------------ | ------------ | ------------ 
recvWindow | LONG | NO |  | 
timestamp | LONG | YES |  | 

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

## Extend Listen Key validity period (USER_STREAM)

`PUT /api/v1/userDataStream`

Validity period has been extended by 60 minutes after this call

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description |  Demo
------------ | ------------ | ------------ | ------------ | ------------ 
listenKey | STRING | YES | Listen Key | eBjOsjYmu1WD6oZHCRpMeWm4UzMvQelhOc9yTD4c
recvWindow | LONG | NO |  | 
timestamp | LONG | YES |  | 

>**Response:**

```javascript
{
  "code": 0,
  "msg": "success"
}
```

## Disable Listen Key (USER_STREAM)

`DELETE /api/v1/userDataStream  (HMAC SHA256)`

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description |  Demo
------------ | ------------ | ------------ | ------------ | ------------ 
recvWindow | STRING | YES | Listen Key | eBjOsjYmu1WD6oZHCRpMeWm4UzMvQelhOc9yTD4c
recvWindow | LONG | NO |  | 
timestamp | LONG | YES |  | 

>**Response:**

```javascript
{
  "code": 0,
  "msg": "success"
}
```


# Account and trading interface

## Account asset info (USER_DATA)

`GET /api/v1/account  (HMAC SHA256)`

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description |  Demo
------------ | ------------ | ------------ | ------------ | ------------ 
recvWindow | LONG | NO |  | 
timestamp | LONG | YES |  | 

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

## Funds transfer (USER_DATA)

`POST /api/v1/transfer  (HMAC SHA256)`

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

>**Response:**

```javascript
{
  "code": 0,
  "msg": "success",
  "data": 4         //  Transfer record ID
}
```

## Option holdings info (USER_DATA)

`GET /api/v1/position  (HMAC SHA256)`

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description |  Demo
------------ | ------------ | ------------ | ------------ | ------------ 
symbol | STRING | NO | Option trading pair | BTCUSDT-200730-9000-C
recvWindow | LONG | NO |  | 
timestamp | LONG | YES |  | 

>**Response:**

```javascript
{
  "code": 0,
  "msg": "success",
  "data": [
    {
      "entryPrice": 1000,               // Average entry price
      "symbol": "BTCUSDT-200730-9000-C",// Option trading pair
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

## Account funding flow (USER_DATA)

`POST /api/v1/bill  (HMAC SHA256)`

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

## Option order (TRADE)

`POST /api/v1/order  (HMAC SHA256)`

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description |  Demo
------------ | ------------ | ------------ | ------------ | ------------ 
symbol | STRING | YES | Option trading pair | BTCUSDT-200730-9000-C
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


>**Response:**

```javascript
{
  "code": 0,
  "msg": "success",
  "data": {
    "id": "4611875134427365377",        // System order number
    "symbol": "BTCUSDT-200730-9000-C",  // Option trading pair
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

## Cancel Option order (TRADE)

`DELETE /api/v1/order  (HMAC SHA256)`

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description |  Demo
------------ | ------------ | ------------ | ------------ | ------------ 
symbol | STRING | YES | Option trading pair | BTCUSDT-200730-9000-C
orderId | LONG | NO | Order ID | 4611875134427365377
clientOrderId | STRING | NO | User-defined order ID | 10000
recvWindow | LONG | NO |  | 
timestamp | LONG | YES |  | 

At least one instance of orderId and clientOrderId must be sent.

>**Response:**

```javascript
{
  "code": 0,
  "msg": "success",
  "data": {
    "id": "4611875134427365377",        // System order number
    "symbol": "BTCUSDT-200730-9000-C",  // Option trading pair
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

## Cancel all Option orders (TRADE)

`DELETE /api/v1/allOpenOrders (HMAC SHA256)`

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description |  Demo
------------ | ------------ | ------------ | ------------ | ------------ 
symbol | STRING | YES | Option trading pair | BTCUSDT-200730-9000-C
recvWindow | LONG | NO |  | 
timestamp | LONG | YES |  | 

>**Response:**

```javascript
{
  "code": 0,
  "msg": "success"
}
```

## Query Option order (TRADE)

`GET /api/v1/order (HMAC SHA256)`

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description |  Demo
------------ | ------------ | ------------ | ------------ | ------------ 
symbol | STRING | YES | Option trading pair | BTCUSDT-200730-9000-C
orderId | LONG | NO | Order ID | 4611875134427365377
clientOrderId | STRING | NO | User-defined order ID | 10000
recvWindow | LONG | NO |  | 
timestamp | LONG | YES |  | 

At least one instance of orderId and clientOrderId must be sent.

>**Response:**

```javascript
{
  "code": 0,
  "msg": "success",
  "data": {
    "id": "4611875134427365377",        // System order number
    "symbol": "BTCUSDT-200730-9000-C",  // Option trading pair
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

## Query current pending Option orders (TRADE)

`GET /api/v1/openOrders  (HMAC SHA256)`

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description |  Demo
------------ | ------------ | ------------ | ------------ | ------------ 
symbol | STRING | YES | Option trading pair | BTCUSDT-200730-9000-C
orderId | LONG | NO | Returns the orderId and subsequent orders, the most recent order is returned by default | 100000
startTime | LONG | NO | Start Time | 1593511200000
endTime | LONG | NO | End Time | 1593512200000
limit | INT | NO | Number of result sets returned Default:100 Max:1000 | 100
recvWindow | LONG | NO |  | 
timestamp | LONG | YES |  | 

>**Response:**

```javascript
{
  "code": 0,
  "msg": "success",
  "data": [
    {
      "id": "4611875134427365377",        // System order number
      "symbol": "BTCUSDT-200730-9000-C",  // Option trading pair
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

## Query Option order history (TRADE)

`GET /api/v1/historyOrders  (HMAC SHA256)`

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description |  Demo
------------ | ------------ | ------------ | ------------ | ------------ 
symbol | STRING | YES | Option trading pair | BTCUSDT-200730-9000-C
orderId | LONG | NO | Returns the orderId and subsequent orders, the most recent order is returned by default | 100000
startTime | LONG | NO | Start Time | 1593511200000
endTime | LONG | NO | End Time | 1593512200000
limit | INT | NO | Number of result sets returned Default:100 Max:1000 | 100
recvWindow | LONG | NO |  | 
timestamp | LONG | YES |  | 

>**Response:**

```javascript
{
  "code": 0,
  "msg": "success",
  "data": [
    {
      "id": "4611875134427365377",        // System order number
      "symbol": "BTCUSDT-200730-9000-C",  // Option trading pair
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



## Push Websocket account info

 Base URL of the API interface listed in this article: https://api.binance.com 
 A User Data Stream listenKey is valid for 60 minutes after creation.
 You can extend the validity period by 60 minutes by performing a PUT listenKey operation.
 You can immediately terminate the current data stream by performing a DELETE listenKey operation, which will render that listenKey invalid.
 Performing a POST operation on an account with a valid listenKey will return the current valid listenKey and extend its validity period by 60 minutes. 
 The baseurl of the websocket interface is: wss://stream.binance.com:9443 
 User Data Streams are accessed at /ws/ or /stream?streams=
 Each link can be valid for no more than 24 hours. Please be sure to handle disconnection/reconnection appropriately.
 Account data stream info is not guaranteed to be in strict chronological order. Please use the E field for sorting.
 
 Data is compressed as a GZIP file by default. After successful connection, send {"method":"BINARY", "params":["false"], "id":1} to convert to text data.

### Generate Listen Key (USER_STREAM)
Start a new data stream. The data stream will be terminated after 60 minutes unless sending keepalive. If the account has a valid listenKey, the listenKey will be returned and the validity period will be extended by 60 minutes.

`GET /v1/private/user/userDataStream`

**Weight:**
1

**Parameters:**
NONE

>**Response:**

```javascript
{
  "listenKey":"pqia91ma19a5s61cv6a81va65sdf19v8a65a1a5s61cv6a81va65sdf19v8a65a1"
}
```
### Extend Listen Key validity period (USER_STREAM)
The validity period will be extended until 60 minutes after this call. It is recommended to send a put request every 30 minutes.

`PUT /v1/private/user/userDataStream`

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description |  Demo
------------ | ------------ | ------------ | ------------ | ------------ 
listenKey | STRING | YES | listenKey | "pqia91ma19a5s61cv6a81va65sdf19v8a65a1a5s61cv6a81va65sdf19v8a65a1"

>**Response:**

```javascript
{
  "listenKey":"pqia91ma19a5s61cv6a81va65sdf19v8a65a1a5s61cv6a81va65sdf19v8a65a1"
}
```
### Delete Listen Key (USER_STREAM)
Start a new data stream. The data stream will be terminated after 60 minutes unless sending keepalive. If the account has a valid listenKey, the listenKey will be returned and the validity period will be extended by 60 minutes.

`DELETE /v1/private/user/userDataStream`

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description |  Demo
------------ | ------------ | ------------ | ------------ | ------------ 
listenKey | STRING | YES | listenKey | "pqia91ma19a5s61cv6a81va65sdf19v8a65a1a5s61cv6a81va65sdf19v8a65a1"


>**Response:**

```javascript
{}
```
### Payload: Account data
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
### Payload: Order data
Update under the following conditions: Order change, order placed, order cancelled, trade completed 


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
### Public Data Payload: 24-hour TICKER
#### Subscribe to an info stream
Request
```javascript
{
"method": "SUBSCRIBE",
"params":
[
"BTCUSDT-200630-9000-P@ticker"
],
"id": 1
}
```
24ticker
>**Response:**

```javascript
{
   "e":"ticker",                // event type
   "E":1591677962357,           // event time
   "s":"BTCUSDT-200630-9000-P", // currency
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
### Public Data Payload: Latest completed trades

Request
```javascript
{
"method": "SUBSCRIBE",
"params":
[
"BTCUSDT-200630-9000-P@trade"
],
"id": 1
}
```

>**Response:**

```javascript
{
    "e":"trade",                        // event type
    "E":1591677941092,                  // event time
    "s":"BTCUSDT-200630-9000-P",        // currency
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

### Public Data Payload: Candle

Request
```javascript
{
"method": "SUBSCRIBE",
"params":
[
"BTCUSDT-200630-9000-P@kline_1m"
],
"id": 1
}
```
```
Cycle
```html
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
```
>**Response:**

```javascript
{
    "e":"kline",                        // event type
    "E":1591677941085,                  // event time
    "s":"BTCUSDT-200630-9000-P",        // currency
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
### Public Data Payload: Depth
Request
```javascript
{
"method": "SUBSCRIBE",
"params":
[
"BTCUSDT-200630-9000-P@depth5"
],
"id": 1
}

```
Cycle
```html
"5", 
"10", 
"20", 
"50",
"100"
"1000"
```
>**Response:**

```javascript
{
    "e":"depth",                    // event type
    "E":1591695934033,              // event time
    "s":"BTCUSDT-200630-9000-P",    // currency
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


