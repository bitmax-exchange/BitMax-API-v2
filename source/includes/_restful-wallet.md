# RESTful APIs - Wallet(Deposit and Withdraw)



## Get Deposit address of one Asset

> Response

```json
{
  "code": 0,
  "data": [
    {
     "asset": "USDT",
     "blockChain": "Omni",
     "address": "USDT-vK3FjAbJ2xOq1Hq7QdHxcRXk4E4UKbE1"
    },
    {
     "asset": "USDT",
     "blockChain": "ERC20",
     "address": "ETH-tlz71zR6KUoQnBSUoTYJG3oJAscqusiK"
    }
  ],
  "email": "xxx@xxx.com",
  "status": "success" // the request has been submitted to the server
}
 ```

**HTTP Request**

`POST <account-group>/api/v2/deposit?asset=<asset>`

**Authentication**

You must have view permission enabled for the API key.

You must sign message `<timestamp>+deposit` and included the signature in the header.

**Parameters**

Name   | Data Type | Description
------ | --------- | -----------------
asset  | String    | Asset code, e.g. `"BTC"`







