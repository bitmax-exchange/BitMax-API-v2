## WebSocket for Cash Trading

<aside class="notice">This API is in beta version and is subject to change. </aside>

**WebSocket URL**

`wss://bitmax.io/<account-group>/api/stream/cash-beta/<symbol>`


### Data Channel - Market Depth

> Market Depth Data

```json
{
   "m":      "depth",
   "s":      "ETH/BTC",
   "ts":     1557422548511,
   "seqnum": 604599926,
   "asks": [
       ["13.45", "59.16"],
       ["13.37", "95.04"],   
       // ...
   ],
   "bids": [
       ["13.21", "60.17"],
       ["13.10", "13.39"],
       // ...
   ]
}
```

Each market depth message is a JSON object containing the current quantity at specific prices levels. There is no direct way of getting the top-of-the-book data through websocket. 
You need to maintain the current depth book and derive the best bid/ask. This can be done by two steps:

* Use the first depth message to build the initial depth book.
* Use later messages to update the depth book. Messages contain the new total size at the indicated price level. 
  You should replace the old quantity using message received. When the replacement quantity is zero, it means there 
  is no order sitting on the corresponding price level.

Each market depth message contains the following field:

Field    | Data Type | Description
-------- | --------- | ---------------------------------------
`m`      | String    | message type, `"depth"` 
`s`      | String    | symbol 
`ts`     | Long      | timestamp when the message is generated
`seqnum` | Long      | sequence number 
`asks`   | Sequence  | ask levels, each element in the sequence contains `[price, quantity]`. `price` and `quantity` are of String type. 
`bids`   | Sequence  | bid levels


### Data Channel - Market Trades 

> Market Trades Data

```json
{
  "m": "marketTrades",
  "s": "ETH/BTC",
  "trades": [
    {
      "p":  "13.75",
      "q":  "6.68",
      "t":  1528988084944,
      "bm": false
    },
    {
      "p":  "13.75",
      "q":  "6.68",
      "t":  1528988084944,
      "bm": false
    },
    // ...
  ]
}
```

Each start receiving continuous market trade stream. All market trades messages follow the same structure, which contains one or more trades.

Each market depth message contains the following field:

Field    | Data Type | Description
-------- | --------- | ---------------------------------------
`m`      | String    | message type, `"marketTrades"`
`s`      | String    | symbol 
`trades` | Sequence  | List of objects containing recent trade data (see below)


Each element in `trades` is an object containing recent trade data with the following fields:

Field    | Data Type | Description
-------- | --------- | ---------------------------------------
`p`      | String    | price
`q`      | String    | quantity 
`t`      | Long      | timestamp 
`bm`     | Boolean   | if `true`, the buyer of the trade is the market maker 



### Data Channel - Market Summary

> Market Summary Data

```json
{
  "m":  "summary",
  "s":  "ETH/BTC",
  "ba": "ETH",
  "qa": "BTC",
  "i":  "1d",
  "t":  1528988000000,
  "o":  "3.24",
  "c":  "3.56",
  "h":  "3.77",
  "l":  "3.21",
  "v":  "10.234"
}
```

Each market summary data record contains current information about a single product. The data is streamed in batches - we stream out market data of all products every 30 seconds. Set `skipSummary = true` 
in the subscribe message to turn it on.


Field    | Data Type | Description
-------- | --------- | ---------------------------------------
  `m`    | String    | "summary"
  `s`    | String    | product symbol
  `ba`   | String    | base asset
  `qa`   | String    | quote asset 
  `i`    | String    | bar interval, for market summary data, the interval is always `1d`
  `t`    | Long      | UTC timestamp in milliseconds
  `o`    | String    | open
  `c`    | String    | close
  `h`    | String    | high
  `l`    | String    | low
  `v`    | String    | volume


### Data Channel - Bar Data

> Bar Chart Data

```json
{
  "m":  "bar",
  "s":  "ETH/BTC",
  "ba": "ETH",
  "qa": "BTC",
  "i":  "5",
  "t":  1528988500000, 
  "o":  "3.24",
  "c":  "3.56",
  "h":  "3.77",
  "l":  "3.21",
  "v":  "10.234" 
}
```

Bar data is almost the same as the market summary data, except that:

Message type is bar
There is only one symbol per websocket session
The interval field i may take multiple values: 1, 5, 30, 160, 360, 1d.
We stream bar data in batches. Every 30 seconds, we stream bar data messages at all interval levels. You may use these data to update bar chart directly (replace bars). However, you should also update the bar chart using the market trade messages.


Field    | Data Type | Description
-------- | --------- | ---------------------------------------
  `m`    | String    | `"bar"`
  `s`    | String    | symbol, e.g. `"BTC/USDT"`
  `ba`   | String    | base asset
  `qa`   | String    | quote asset 
  `i`    | String    | bar interval, e.g. `"1", "5", "30", "60", "360", "1d"`
  `t`    | Long      | UTC timestamp in milliseconds
  `o`    | String    | open
  `c`    | String    | close
  `h`    | String    | high
  `l`    | String    | low
  `v`    | String    | volume


### Data Channel - Order Updates

@ToDo


### Data Channel - Balance Updates

@ToDo


### Client Request - Placing New Order 

> New Order Request

```json
{
  "messageType": "newOrderRequest",
  "time":        1528988500000,
  "coid":        "G4afqeetOz34Wh4SqtJUye6vpYqhe7TH",
  "symbol":      "BTC/USDT",
  "orderPrice":  "5000",
  "orderQty":    "0.35",
  "orderType":   "limit",
  "side":        "buy"
}
```

You can place orders by sending `newOrderRequest` messages to the server.

Key           | Data Type | Value
------------- | --------- | ---------------------------------------------
`messageType` | String    | required, `"newOrderRequest"`
`time`        | Long      | required, UTC timestamp in milliseconds
`coid`        | String    | required, unique ID string of length 32
`symbol`      | String    | required, symbol, e.g. `"BTC/USDT"`
`orderPrice`  | String    | optional, the limit price of the order
`orderQty`    | String    | required, the order quantity as a string, e.g. "1.23"
`orderType`   | String    | required, `"limie"`, `"market"`, `"stop_limit"`, `"stop_market"`
`side`        | String    | 
`postOnly`    | Boolean   | Optional, `true` or `false`
`stopPrice`   | String    | the stop price as a string, e.g `"10000.50"`
`timeInForce` | String    | `GTC` (Good-till-canceled, default) or `IOC` (immediate-or-cancel)


> Invalid Order Rejection Message

```json 
{
  "m":         "order",
  "coid":      "G4afqeetOz34Wh4SqtJUye6vpYqhe7TH",
  "status":    "Rejected",
  "errorCode": "600533",
  "reason":    "Price is too low from market price."
}
```

If the order is invalid, you will receive an rejection message immediately with `coid` the same as the new order request message. 

Key           | Data Type | Value
------------- | --------- | ---------------------------------------------
`m`           | String    | `"order"`
`coid`        | String    | the `coid` in the order request 
`status`      | String    | `"Rejected"`
`errorCode`   | String    | a numeric error code as a string. (We use string format to be consistent with other order messages)
`reason`      | String    | 


### Client Request - Cancel an Order

> Cancel an Order

```json
{
  "messageType": "cancelOrderRequest",
  "time":        1528988100000, 
  "coid":        "Zgu7EabVbGNnnGsCid0MydDA4pa7ighE",
  "origCoid":    "dHKTlw3Mh4W4Zg9RjkU5cnFopwjC9uWU",
  "symbol":      "BTC/USDT"
}
```

@ToDo

Key           | Data Type | Value
------------- | --------- | ---------------------------------------------
`messageType` | String    | required, `"newOrderRequest"`
`time`        | Long      | required, UTC timestamp in milliseconds
`coid`        | String    | required, unique ID string of length 32
`origCoid`    | String    | required, the original `coid` of the order 
`symbol`      | String    | required, symbol, e.g. `"BTC/USDT"`



