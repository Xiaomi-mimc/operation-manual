## 历史消息

### 参数列表

|   Variable   | Meanings  |
| :----------------- | :---------------------------------------------------------|
|  $appId            |  小米开放平台申请的AppId                            |
|  $token            |  查询方的token（使用user.getToken()获取）                    |
|  $account          |  查询方在APP系统内唯一ID                                    |
|  $fromAccount      |  消息发送方在APP帐号系统内唯一ID                             |
|  $toAccount        |  消息接收方在APP帐号系统内唯一ID                             |
|  $topicId          |  表示群ID                                              |
|  $utcFromTime      |  表示查询开始时间，UTC时间，单位毫秒                   |
|  $utcToTime        |  表示查询结束时间，UTC时间，单位毫秒                   |
|  $startSeq         |  表示查询开始序列号                   |
|  $stopSeq          |  表示查询结束序列号                   |
|  $count            |  表示查询的消息条数                                 |
|  $row              |  表示返回的消息条数                                 |
|  $timestamp        |  表示返回的消息中最早的时间戳                         |
|  $messages         |  表示返回的消息集合                                 |
|  $sequence         |  sequence主要用来做消息的排序和去重，全局唯一           |
|  $payload         |  表示经过Base64编码的消息体，app端需要进行Base64解码          |
|  $ts               |  表示消息时间戳                                      |

#### 备注：

```
消息漫游为用户保存最近半年的历史消息
```

### 拉取单聊消息记录

```
指的是拉取从utcFromTime到utcToTime的时间范围内的A与B之间的聊天记录，单聊是相对于群聊而言的一对一聊天。
```

#### 如下为拉取[utcFromTime, utcToTime)的单聊消息记录

+ HTTPS请求(POST)

```
curl https://mimc.chat.xiaomi.net/api/msg/p2p/query/ -XPOST
  -d '{"appId":$appId,"toAccount":$toAccount,"fromAccount":$fromAccount,"utcFromTime":$utcFromTime,"utcToTime":$utcToTime}'
  -H "Content-Type: application/json;charset=UTF-8"
  -H "Accept:application/json;charset=UTF-8"
  -H "token:$token"
```
```
curl https://mimc.chat.xiaomi.net/api/msg/p2p/queryOnTime -XPOST
  -d '{"appId":$appId,"toAccount":$toAccount,"fromAccount":$fromAccount,"utcFromTime":$utcFromTime,"utcToTime":$utcToTime}'
  -H "Content-Type: application/json;charset=UTF-8"
  -H "Accept:application/json;charset=UTF-8"
  -H "token:$token"
```

+ JSON结果示例

```
{
     "code": 200,
     "message": "success",
     "data": {
         "appId": $appId,
         "messages": [
             {
                 "sequence": $sequence,
                 "payload": $payload,
                 "ts": $ts,
                 "fromAccount":$fromAccount,
                 "toAccount": $toAccount,
             }
         ],
         "row": $row
     }
 }
```

#### 备注

```
utcFromTime和utcToTime的时间间隔不能超过24小时，查询状态为[utcFromTime,utcToTime);
timestamp字段在这个请求的响应中没有意义。
```

### 拉取指定数目单聊消息记录

```
指的是拉取从指定的时间戳utcToTime(不包含utcToTime)向前count条的A与B之间的聊天记录。
```

#### 如下为拉取utcToTime向前count条的单聊消息记录

+ HTTPS请求(POST)

```
curl https://mimc.chat.xiaomi.net/api/msg/p2p/queryOnCount/ -XPOST
  -d '{"appId":$appId,"toAccount":$toAccount,"fromAccount":$fromAccount,"utcToTime":$utcToTime,"count":$count}'
  -H "Content-Type: application/json;charset=UTF-8"
  -H "Accept:application/json;charset=UTF-8"
  -H "token:$token"
```

+ JSON结果示例

```
{
     "code": 200,
     "message": "success",
     "data": {
         "appId": $appId,
         "messages": [
             {
                 "sequence": $sequence,
                 "payload": $payload,
                 "ts": $ts,
                 "fromAccount":$fromAccount,
                 "toAccount": $toAccount,
             }
         ],
         "row": $row,
         "timestamp":$timestamp
     }
 }
```

#### 备注

```
timestamp字段在这个请求的响应中表示当前的聊天记录最早的时间戳(单位：毫秒)。
```

### 拉取指定序列号单聊消息记录

```
指的是拉取从startSeq到stopSeq之间的A与B之间的聊天记录。
```

#### 如下为拉取[startSeq, stopSeq)的单聊消息记录

+ HTTPS请求(POST)

```
curl https://mimc.chat.xiaomi.net/api/msg/p2p/queryOnSequence/ -XPOST
  -d '{"appId":$appId,"toAccount":$toAccount,"fromAccount":$fromAccount,"startSeq":$startSeq,"stopSeq":$stopSeq}'
  -H "Content-Type: application/json;charset=UTF-8"
  -H "Accept:application/json;charset=UTF-8"
  -H "token:$token"
```

+ JSON结果示例

```
{
     "code": 200,
     "message": "success",
     "data": {
         "appId": $appId,
         "messages": [
             {
                 "sequence": $sequence,
                 "payload": $payload,
                 "ts": $ts,
                 "fromAccount":$fromAccount,
                 "toAccount": $toAccount,
             }
         ],
         "row": $row,
         "timestamp":$timestamp
     }
 }
```

#### 备注
```
timestamp字段表示当前的聊天记录最早的时间戳(单位：毫秒)。
```

### 拉取群聊消息记录

#### 如下为拉取群聊消息记录

+ HTTPS请求(POST)

```
curl https://mimc.chat.xiaomi.net/api/msg/p2t/query/ -XPOST
  -d '{"appId":$appId,"account":$account,"topicId":$topicId,"utcFromTime":$utcFromTime,"utcToTime":$utcToTime}'
  -H "Content-Type: application/json;charset=UTF-8"
  -H "Accept:application/json;charset=UTF-8"
  -H "token:$token"

curl https://mimc.chat.xiaomi.net/api/msg/p2t/queryOnTime/ -XPOST
  -d '{"appId":$appId,"account":$account,"topicId":$topicId,"utcFromTime":$utcFromTime,"utcToTime":$utcToTime}'
  -H "Content-Type: application/json;charset=UTF-8"
  -H "Accept:application/json;charset=UTF-8"
  -H "token:$token"
```
```
PS：URL中的$account与$token需要相匹配（即$token应该是$account用户的token信息）。
```

+ JSON结果示例

```
{
     "code": 200,
     "message": "success",
     "data": {
         "appId": $appId,
         "topicId": $topicId,
         "row": $row,
         "messages": [
             {
                 "sequence": $sequence,
                 "fromAccount": $fromAccount,
                 "payload": $payload,
                 "ts": $ts
             },
             {
                 "sequence": $sequence,
                 "fromAccount": $fromAccount,
                 "payload": $payload,
                 "ts": $ts
             }
         ]
     }
 }
```

#### 备注

```
timestamp字段在这个请求的响应中没有意义。
```

### 拉取指定数目群聊消息记录

```
指的是拉取从指定的时间戳utcToTime(不包含utcToTime)向前count条的指定的topicId的群聊天记录。
```

#### 如下为拉取utcToTime向前count条的群聊消息记录

+ HTTPS请求(POST)

```
curl https://mimc.chat.xiaomi.net/api/msg/p2t/queryOnCount/ -XPOST
  -d '{"appId":$appId,"account":$account,"topicId":$topicId,"utcToTime":$utcToTime,"count":$count}'
  -H "Content-Type: application/json;charset=UTF-8"
  -H "Accept:application/json;charset=UTF-8"
  -H "token:$token"
```
```
PS：URL中的$account与$token需要相匹配（即$token应该是$account用户的token信息）。
```

+ JSON结果示例
```
{
     "code": 200,
     "message": "success",
     "data": {
         "appId": $appId,
         "messages": [
             {
                 "sequence": $sequence,
                 "payload": $payload,
                 "ts": $ts,
                 "fromAccount":$fromAccount,
             }
         ],
         "row": $row,
         "timestamp":$timestamp
     }
 }
```
#### 备注
```
timestamp字段在这个请求的响应中表示当前的聊天记录最早的时间戳(单位：毫秒)。
```

### 拉取指定序列号群聊消息记录
```
指的是拉取从startSeq到stopSeq之间的指定的topicId的群聊天记录。
```

#### 如下为拉取[startSeq, stopSeq)的群聊消息记录

+ HTTPS请求(POST)
```
curl https://mimc.chat.xiaomi.net/api/msg/p2t/queryOnSequence/ -XPOST
  -d '{"appId":$appId,"account":$account,"topicId":$topicId,"startSeq":$startSeq,"stopSeq":$stopSeq}'
  -H "Content-Type: application/json;charset=UTF-8"
  -H "Accept:application/json;charset=UTF-8"
  -H "token:$token"
```
```
PS：URL中的$account与$token需要相匹配（即$token应该是$account用户的token信息）。
```

+ JSON结果示例
```
{
     "code": 200,
     "message": "success",
     "data": {
         "appId": $appId,
         "messages": [
             {
                 "sequence": $sequence,
                 "payload": $payload,
                 "ts": $ts,
                 "fromAccount":$fromAccount,
                 "toAccount": $toAccount,
             }
         ],
         "row": $row,
         "timestamp":$timestamp
     }
 }
```

#### 备注
```
timestamp字段表示当前的聊天记录最早的时间戳(单位：毫秒);
fromAccount字段表示当前消息所在群的topicId。
```