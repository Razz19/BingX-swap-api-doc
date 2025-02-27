Bingx官方API文档
==================================================
Bingx开发者文档([English Docs](./Perpetual_Swap_WebSocket_Market_Interface.md))。

<!-- TOC -->

- [Websocket 介绍](#Websocket-介绍)
    - [接入方式](#接入方式)
    - [数据压缩](#数据压缩)
    - [心跳信息](#心跳信息)
    - [订阅方式](#订阅方式)
    - [取消订阅](#取消订阅)
- [Websocket 行情推送](#Websocket-行情推送)
    - [订阅合约交易深度](#1-订阅合约交易深度)
    - [订单最新成交记录](#2-订单最新成交记录)
    - [订阅合约k线数据](#3-订阅合约k线数据)
- [Websocket 账户信息推送](#Websocket-账户信息推送)
  - [订阅资产更新数据](#1-订阅资产更新数据)

<!-- /TOC -->

# Websocket 介绍

## 接入方式

行情Websocket的接入URL：`wss://open-ws-swap.bingbon.pro/ws`

## 数据压缩

WebSocket 行情接口返回的所有数据都进行了 GZIP 压缩，需要 client 在收到数据之后解压。

## 心跳信息

当用户的Websocket客户端连接到Bingx Websocket服务器后，服务器会定期（当前设为5秒）向其发送心跳字符串Ping，

当用户的Websocket客户端接收到此心跳消息后，应返回字符串Pong消息

## 订阅方式

成功建立与Websocket服务器的连接后，Websocket客户端发送如下请求以订阅特定主题：

{
  "id": "id1",
  "reqType": "sub",
  "dataType": "data to sub",
}

成功订阅后，Websocket客户端将收到确认：

{
  "id": "id1",
  "code": 0,
  "msg": "",
}
之后, 一旦所订阅的数据有更新，Websocket客户端将收到服务器推送的更新消息

## 取消订阅
取消订阅的格式如下：

{
  "id": "id1",
  "reqType": "unsub",
  "dataType": "data to unsub",
}

取消订阅成功确认：

{
  "id": "id1",
  "code": 0,
  "msg": "",
}


# Websocket 行情推送

## 1. 订阅合约交易深度

    订阅合约对盘口深度的数据的推送，此主题发送最新市场深度快照。快照频率为每秒1次。

**订阅类型**

    dataType 为 market.depth.$Symbol.$Step.$Level，比如market.depth.BTC-USDT.step0.level5

**订阅参数**  


| 参数名 | 参数类型  | 必填 | 描述 |
| ------------- |----|----|----|
| symbol | String | 是 | 合约名称中需有"-"，如BTC-USDT |
| step | String | 是 | 合并深度类型，step0，step1，step2，step3，step4，step5 |
| level | String | 是 | 档数, 如 level5，level10，level20，level50，level100 |

**备注**

"step" 合并深度类型
| 参数名 | 描述 |
| ----- |----|
| step0 | 不合并深度 |
| step1 | 按价格最小精度乘以10合并深度数据 |
| step2 | 按价格最小精度乘以100合并深度数据 |
| step3 | 按价格最小精度乘以1000合并深度数据 |
| step4 | 按价格最小精度乘以10000合并深度数据 |
| step5 | 按价格最小精度乘以100000合并深度数据 |

"level" 深度档数定义
| 参数名 | 描述 |
| ----- |----|
| level5 | 5档 |
| level10 | 10档 |
| level20 | 20档 |
| level50 | 50档 |
| level100 | 100档 |

**推送数据** 

| 返回字段|字段说明|  
| ------------- |----|
| code   | 是否有错误信息，0为正常，1为有错误 |
| dataType | 订阅的数据类型，例如 market.depth.BTC-USDT.step0.level5 |
| data | 推送内容 |
| asks   | 卖方深度 |
| bids   | 买方深度 |
| p      | price价格  |
| v      | volume数量 | 

```javascript
    # Response
    {
        "code": 0,
        "dataType": "market.depth.BTC-USDT.step0.level5",
        "data": {
            "asks": [
                {
                    "p": 5319.94,
                    "v": 0.05483456
                },{
                    "p": 5320.19,
                    "v": 1.05734545
                },{
                    "p": 5320.39,
                    "v": 1.16307999
                },{
                    "p": 5319.94,
                    "v": 0.05483456
                },{
                    "p": 5320.19,
                    "v": 1.05734545
                },{
                    "p": 5320.39,
                    "v": 1.16307999
                },
            ],
            "bids": [
                {
                    "p": 5319.94,
                    "v": 0.05483456
                },{
                    "p": 5320.19,
                    "v": 1.05734545
                },{
                    "p": 5320.39,
                    "v": 1.16307999
                },{
                    "p": 5319.94,
                    "v": 0.05483456
                },{
                    "p": 5320.19,
                    "v": 1.05734545
                },{
                    "p": 5320.39,
                    "v": 1.16307999
                },
            ],
        }
    }
```


## 2. 订单最新成交记录

    订阅单个合约的逐笔成交明细

**订阅类型**

    dataType 为 market.trade.detail.$Symbol，比如market.trade.detail.BTC-USDT

**订阅参数**

| 参数名  | 参数类型  | 必填 | 字段描述 | 描述 |
| -------|--------|--- |-------|------|
| symbol | String | 是 |合约名称| 合约名称中需有"-"，如BTC-USDT |

**推送数据** 

| 返回字段|字段说明|  
| ------------- |----|
| code   | 是否有错误信息，0为正常，1为有错误 |
| dataType | 订阅的数据类型，例如 market.tradeDetail.BTC-USDT |
| data | 推送内容 |
| trades    | 逐笔成交 |
| time      | 成交时间 |
| makerSide | 吃单方向(Bid / Ask 买/卖) |
| price     | 成交价格 |
| volume    | 成交数量 |

   ```javascript
    # Response
    {
        "code": 0,
        "dataType": "market.tradeDetail.BTC-USDT",
        "data": {
            "trades": [
                {
                    "time": "2018-04-25T15:00:51.999Z",
                    "makerSide": "Bid",
                    "price": 0.279563,
                    "volume": 100,
                },
                {
                    "time": "2018-04-25T15:00:51.000Z",
                    "makerSide": "Ask",
                    "price": 0.279563,
                    "volume": 300,
                }
            ]
        }
    }
   ```

## 3. 订阅合约k线数据

    订阅单个合约的行情k线数据

**订阅类型**

    dataType 为 market.kline.$Symbol.$KlineType，比如market.kline.BTC-USDT.1min

**订阅参数**

| 参数名  | 参数类型  | 必填 | 字段描述 | 描述 |
| -------|--------|--- |-------|------|
| symbol | String | 是 |合约名称| 合约名称中需有"-"，如BTC-USDT |
| klineType | String | 是 |k线类型| 参考字段说明，如分钟，小时，周等 |

**备注**

| klineType 字段说明 |     |
|----------------|-----|
| 1min           | 一分钟K线 |
| 3min           | 三分钟K线 |
| 5min           | 五分钟K线 |
| 15min          | 十五分钟K线 |
| 30min          | 三十分钟K线 |
| 1hour          | 一小时K线 |
| 2hour          | 两小时K线 |
| 4hour          | 四小时K线 |
| 6hour          | 六小时K线 |
| 12hour         | 十二小时K线 |
| 1day           | 日K线 |
| 1week          | 周K线 |
| 1month         | 月K线 |

**推送数据** 

| 返回字段|字段说明|  
| ------------- |----|
| code   | 是否有错误信息，0为正常，1为有错误 |
| data | 推送内容 |
| dataType | 数据类型 |
| klineInfosVo    | K线数据 |
| close      | 收盘价 |
| high    | 最高价 |
| low     | 最低价 |
| open    | 收盘价 |
| statDate    | k线时间 |
| time    | k线时间戳，单位是毫秒 |
| volume    | 成交量 |

   ```javascript
    # Response
    {
        "code": 0,
        "data": {
            "klineInfosVo": [
                {
                    "close": 54564.31, 
                    "high": 54711.73,
                    "low": 54418.27,
                    "open": 54577.41, 
                    "statDate": "2021-04-29T11:00:00.000+0800", 
                    "time": 1619665200000, 
                    "volume": 1607.0727000000002
                }
            ]
        },
        "dataType": "market.kline.BTC-USDT.30min" // 数据类型
    }
   ```
# Websocket 账户信息推送

- 注意需要获取此类信息需要 websocket 鉴权，使用 listenKey，详细方式查看 [Rest 接口文档](https://github.com/BingX-API/BingX-swap-api-doc/blob/master/%E4%B8%93%E4%B8%9A%E5%90%88%E7%BA%A6API%E6%8E%A5%E5%8F%A3.md#%E5%85%B6%E4%BB%96%E6%8E%A5%E5%8F%A3)
- websocket接口是 wss://open-ws-swap.bingbon.pro/ws
- 订阅账户数据流的stream名称为 /{listenKey}
```
wss://open-ws-swap.bingbon.pro/ws/94bE3nW8BuyGCUsvjRKPPRt1lDomEeJlEO8ABMLxYM6rT92u
```
## 1. 订阅资产更新数据

**订阅类型**

    dataType 为 ACCOUNT_UPDATE

**订阅例子**

    {"id":"e745cd6d-d0f6-4a70-8d5a-043e4c741b40","dataType":"ACCOUNT_UPDATE"}

**推送数据**

| 返回字段     | 字段说明               |  
|----------|--------------------|
| dataType | 数据类型 |
| data     | 推送内容               |
| E        | 事件时间                |
| e        | 事件类型                |
| a        | 账户更新事件               |
| m        | 事件推出原因                |
| B        | 余额信息                |

- 字段"m"代表了事件推出的原因，包含了以下可能类型:
  - DEPOSIT(入金)
  - WITHDRAW(出金)
  - FUNDING_FEE(资金费用)
  - ORDER(订单变更)
- 字段"bc"代表了钱包余额的改变量，即 balance change，但注意其不包含仓位盈亏及交易手续费。

   ```javascript
    # Response
    {
            "e": "ACCOUNT_UPDATE",                // 事件类型
            "E": 1564745798939,                   // 事件时间
            "a":                                  // 账户更新事件
                {
                "m": "WITHDRAW",                   //事件推出原因
                "B":[                             // 余额信息
                  {
                    "a":"USDT",                   // 资产名称
                    "wb":"4499.53918561",       // 钱包余额
                    "cw":"4470.890393795533",          // 除去逐仓仓位保证金的钱包余额
                    "bc":"-66"            // 除去盈亏与交易手续费以外的钱包余额改变量
              }
          ]
        }
    }
   ```

  **备注**

  更多返回错误代码请看首页的错误代码描述