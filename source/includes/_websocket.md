WebSocket API (Beta Version)
===========================================================

## WebSocket Data Feed 

> websocket URL examples:

```
wss://bitmax.io/3/api/stream/cash-beta/ETH-BTC
wss://bitmax.io/3/api/stream/margin-beta/ETH-BTC
```

**Cash trading data feed** (`api_path=stream/cash-beta`):

`wss://bitmax.io/<account-group>/api/stream/cash-beta/<symbol>`


**Margin trading data feed** (`api_path=stream/cash-beta`):

`wss://bitmax.io/<account-group>/api/stream/margin-beta/<symbol>`


**Note**: `<symbol>` in URLs above must be seperated by hyphen (`-`), e.g, `ETH-BTC`. Symbols separated by slash (`/`) are not allowed. 


### WebSocket Authentication 

Connecting to websocket API follows almost the same authentication process as authenticated RESTful APIs. You need to include the following
fields in the request header:

* `x-auth-key` - the API key with view permission. Trade permission is needed if you want to place orders.
* `x-auth-signature` - the message signed using sha256 using the base64-decoded secret key on the prehash string `<timestamp>+api/stream`.
* `x-auth-timestamp` - the current UTC timestamp in milliseconds.


### Subscribe to WebSocket Streams 

After connecting to websocket, you need to send an `subscribe` message to start receiving data. The subscribe message is a JSON object 
in plain text format and contains the following fields:

> Subscribe Message

```json
{
  "messageType": "subscribe",
  "marketDepthLevel": 20,
  "recentTradeMaxCount": 20,
  "skipSummary": false,
  "skipBars": false
}
```

Field Name            | Data Type | Description
--------------------- | --------- | -----------
`messageType`         | String    | set this field to `subscribe` in the subscribe message.
`marketDepthLevel`    | Integer   | optional, default value 20. This field specifies the max number of price levels on each side to be included in the first market depth message.
`recentTradeMaxCount` | Integer   | optional, default value 20. This field specifies the max number of recent trades to be included in the first market trades message.
`skipSummary`         | Boolean   | optional, default value `false`. If `true`, client will receive market summary data with rolling 24 hour O/H/L/C price data for all symbols every 30 seconds.
`skipBars`            | Boolean   | optional, default value `false`. If `true`, client will receive bar data of all frequencies (1 minute, 5 minutes, etc.) for the current symbol every 30 seconds.

